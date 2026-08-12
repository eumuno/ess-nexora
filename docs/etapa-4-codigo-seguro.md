# 4 — Código Seguro e Testes de Segurança

Esta etapa deverá demonstrar duas práticas de código seguro derivadas dos
riscos e requisitos das etapas anteriores. Os testes são definidos antes da
descrição da implementação.

---

## 4.1 Prática de Código Seguro 1: Autenticação Multifator (MFA) e Limitação de Tentativas (Rate Limiting)

### 4.1.1 Concepção e Casos de Teste
A primeira prática de código seguro foca no endurecimento do fluxo de autenticação da plataforma, protegendo os acessos contra tentativas de força bruta e sequestro de contas com credenciais previamente vazadas na internet.

* **Risco de Origem:** R01 — Uso indevido de conta (Credential Stuffing).
* **Requisito de Segurança Relacionado:** RS01 — Exigência de autenticação multifator (MFA) obrigatória para contas administrativas e de instrutores, além de bloqueio de tentativas de acesso sequenciais inválidas.

Para validar esse comportamento de forma objetiva, definimos os seguintes casos de teste de segurança antes do início do desenvolvimento da solução técnica:

| ID do Teste | Descrição da Entrada / Ação | Resultado Seguro Esperado |
| :--- | :--- | :--- |
| **TS01 — Login Válido com MFA** | Usuário administrativo ou instrutor fornece e-mail e senha corretos e, em seguida, insere o código de 6 dígitos válido gerado pelo seu aplicativo autenticador. | O sistema valida as credenciais e o token temporário, emite o cookie de sessão seguro/token JWT e autoriza o acesso ao painel de controle. |
| **TS02 — Login com Código MFA Inválido** | Usuário fornece e-mail e senha corretos, mas insere um código MFA incorreto, expirado ou em branco. | O servidor bloqueia o login imediatamente, invalida a tentativa, retorna um erro HTTP 401 Unauthorized e registra o evento suspeito nos logs de segurança. |
| **TS03 — Bloqueio por Força Bruta (Rate Limiting)** | Atacante submete 6 requisições consecutivas de login em menos de 15 minutos para o mesmo endpoint utilizando diferentes combinações de senhas vindo do mesmo endereço IP. | O servidor bloqueia a 6ª requisição, interrompe temporariamente o processamento de novas tentativas para aquele IP, retorna o erro HTTP 429 Too Many Requests e ativa o alerta de defesa. |

---

### 4.1.2 Implementação e Referência OWASP

Para mitigar o risco crítico de **Uso indevido de conta por credential stuffing (R01)** e atender diretamente ao requisito de segurança **RS01** estabelecido na arquitetura da Nexora, foi projetada uma lógica de controle de acesso no back-end da plataforma. Essa prática combina a **Limitação de Tentativas (Rate Limiting)** com a **Autenticação Multifator baseada em tempo (TOTP)** para perfis com privilégios administrativos (Instrutores e Administradores).

#### A. Referência Oficial OWASP Utilizada

A lógica foi estruturada seguindo estritamente as diretrizes do **OWASP Multifactor Authentication Cheat Sheet** e do **OWASP ASVS (Application Security Verification Standard)**. As boas práticas aplicadas garantem que:
1. O segundo fator (MFA) é exigido de forma mandatória e validado no servidor para perfis administrativos (Instrutores e Administradores).
2. O mecanismo de *Rate Limiting* atua como um filtro middleware de entrada, bloqueando ataques automatizados de força bruta (*credential stuffing*) antes que o servidor consuma processamento pesado validando hashes de senhas.

#### B. Comportamento do Código e Lógica de Negócio

1. **Filtro de Limitação (Rate Limiting):** O sistema monitora as tentativas de login malsucedidas associadas ao IP do cliente. Se o limite de 5 tentativas inválidas for atingido em menos de 15 minutos, o acesso é bloqueado temporariamente, retornando um erro de requisições excessivas (HTTP 429).
2. **Autenticação Primária (Fase 1):** O usuário envia e-mail e senha. O sistema valida as credenciais contra o hash seguro do banco de dados. Se falhar, incrementa o contador do IP.
3. **Verificação de MFA (Fase 2):** Se as credenciais estiverem corretas, o contador de falhas do IP é limpo. 
   - Se o usuário for **Aluno** ou **Visitante**, o login é concluído de forma comum e o token de sessão (JWT) é retornado.
   - Se o usuário for **Instrutor** ou **Administrador**, o sistema retém a sessão e gera um token temporário restrito apenas para carregar o passo do MFA (expira em 3 minutos).
4. **Validação de TOTP (Fase 3):** O usuário insere o código MFA de 6 dígitos gerado em seu dispositivo. O servidor valida o código usando o segredo criptográfico (`mfaSecret`) armazenado no banco de dados. Sendo válido, o token JWT definitivo de acesso é emitido.

#### C. Pseudocódigo da Implementação (Lógica do Servidor)

```
// ALGORITMO: Controle de Acesso Seguro (MFA e Rate Limiting)

// Função auxiliar para verificar o limite de tentativas de login por IP
FUNCAO verificar_rate_limit(ip_cliente)
    tentativas = obter_registro_tentativas_temporario(ip_cliente)
    
    SE tentativas >= 5 ENTAO
        RETORNAR STATUS_HTTP(429, "Limite de tentativas excedido. Bloqueado por 15 minutos.")
    FIM_SE
FIM_FUNCAO

// ENDPOINT: Login Primário (E-mail e Senha)
FUNCAO post_login(requisicao)
    ip_cliente = requisicao.ip
    email = requisicao.corpo.email
    senha = requisicao.corpo.senha

    // Aplica o filtro de segurança de Rate Limiting
    verificar_rate_limit(ip_cliente)

    // Busca o usuário no banco de dados de forma segura
    usuario = buscar_usuario_por_email(email)
    
    // Validação criptográfica de senha contra o hash armazenado
    SE usuario NAO EXISTE OU verificar_hash_senha(senha, usuario.senha_hash) == FALSO ENTAO
        incrementar_registro_tentativas_temporario(ip_cliente)
        RETORNAR STATUS_HTTP(401, "E-mail ou senha incorretos.")
    FIM_SE

    // Limpa as tentativas acumuladas do IP após sucesso parcial
    limpar_registro_tentativas_temporario(ip_cliente)

    // RS01: Valida se o perfil logado exige MFA obrigatório
    SE usuario.perfil == "Instrutor" OU usuario.perfil == "Administrador" ENTAO
        // Gera um token temporário assinado apenas para a etapa do MFA (válido por 3 minutos)
        token_temporario = gerar_jwt_temporario(usuario.id, expira_em: 180_segundos)
        
        RETORNAR STATUS_HTTP(200, {
            mfaRequired: VERDADEIRO,
            mfaStepToken: token_temporario,
            mensagem: "Autenticação em duas etapas requerida para este perfil administrativo."
        })
    SENAO
        // Login direto sem MFA para Alunos e Visitantes
        token_sessao_definitivo = gerar_jwt_sessao(usuario.id, usuario.perfil)
        RETORNAR STATUS_HTTP(200, { token: token_sessao_definitivo, perfil: usuario.perfil })
    FIM_SE
FIM_FUNCAO

// ENDPOINT: Validação do Código MFA (TOTP)
FUNCAO post_validar_mfa(requisicao)
    ip_cliente = requisicao.ip
    token_temporario = requisicao.corpo.mfaStepToken
    codigo_totp = requisicao.corpo.totpCode

    // Aplica o filtro de segurança de Rate Limiting
    verificar_rate_limit(ip_cliente)

    // Decodifica e valida a integridade do token temporário
    dados_token = decodificar_e_validar_jwt(token_temporario)
    SE dados_token == NULO ENTAO
        RETORNAR STATUS_HTTP(401, "Sessão de MFA temporária inválida ou expirada.")
    FIM_SE

    // Busca o usuário correspondente ao token temporário
    usuario = buscar_usuario_por_id(dados_token.usuario_id)
    SE usuario == NULO ENTAO
        RETORNAR STATUS_HTTP(401, "Usuário não localizado.")
    FIM_SE

    // Validação do código de 6 dígitos gerado pelo usuário usando o segredo criptográfico
    is_codigo_valido = validar_codigo_totp_criptografico(codigo_totp, usuario.mfa_segredo)
    
    SE is_codigo_valido == FALSO ENTAO
        incrementar_registro_tentativas_temporario(ip_cliente) // Registra a falha no IP
        RETORNAR STATUS_HTTP(401, "Código de autenticação multifator inválido ou expirado.")
    FIM_SE

    // Sucesso completo: limpa o IP e emite o token de acesso definitivo (JWT)
    limpar_registro_tentativas_temporario(ip_cliente)
    token_sessao_definitivo = gerar_jwt_sessao(usuario.id, usuario.perfil)
    
    RETORNAR STATUS_HTTP(200, { token: token_sessao_definitivo, perfil: usuario.perfil })
FIM_FUNCAO
```

#### D. Resultado Seguro Esperado

*   **Controle de Força Bruta Ativo:** Se um bot tentar forçar múltiplas requisições de login para tentar descobrir senhas de Alunos ou Instrutores, o IP de origem será bloqueado na 6ª tentativa consecutiva inválida, protegendo os recursos do servidor.
*   **Mitigação de Credenciais Vazadas (Credential Stuffing):** Mesmo que um atacante consiga descobrir as credenciais de e-mail e senha de um Instrutor ou Administrador, ele será barrado na Fase 2 do fluxo lógico. Sem o dispositivo físico do usuário para gerar o TOTP de 6 dígitos na Fase 3, ele é incapaz de obter o token final de sessão do sistema.

---

## 4.2 Prática de Código Seguro 2: Validação de callbacks de pagamento

**Situação:** proposta provisória até a confirmação da Prática 1.

**Risco relacionado:** R04 — Forjamento de notificações de callback de
pagamento.

**Requisito relacionado:** RS03 — Verificação da assinatura HMAC-SHA256 antes
da liberação de qualquer curso.

**Justificativa:** callbacks são entradas externas e não podem ser considerados
confiáveis apenas porque utilizam o endpoint esperado. A API deve verificar a
autenticidade e a integridade da notificação, relacioná-la a um pedido existente
e impedir processamento repetido antes de alterar o estado da compra ou liberar
conteúdo. A prática é sustentada pela [OWASP Third Party Payment Gateway
Integration Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Third_Party_Payment_Gateway_Integration_Cheat_Sheet.html).

---

### 4.2.1 Casos de teste definidos antes da implementação

| ID | Tipo de cenário | Entrada ou ação | Resultado seguro esperado |
| :---: | :--- | :--- | :--- |
| **TS03** | Válido | O gateway envia uma única requisição `POST /api/v1/payments/callback` para um pedido pendente, com assinatura HMAC válida e valores de pedido, moeda e transação correspondentes aos registros do servidor. | A API valida a assinatura com comparação segura, consulta ou confirma o estado da transação, registra o identificador e o resultado sem armazenar chave ou assinatura sensível, altera o pedido para pago uma única vez e libera somente o curso associado. A resposta confirma o processamento sem expor dados internos. |
| **TS04** | Malicioso ou não autorizado | Um agente envia callback sem assinatura, com HMAC inválido, pedido inexistente, valor divergente ou identificador de transação já processado. | A API rejeita a requisição antes de alterar o pedido ou liberar conteúdo. Assinatura ausente ou inválida produz resposta `401 Unauthorized`; divergência ou repetição é recusada de forma idempotente. O evento é registrado com horário, pedido, origem e motivo sanitizado, sem segredo HMAC nem dados financeiros completos, e fica disponível para alerta e investigação. |

---

### 4.2.2 Critérios de aprovação

- TS03 deve comprovar uma transição única e rastreável de pedido pendente para
  pago.
- TS04 deve comprovar que nenhuma variação inválida libera curso ou altera o
  estado financeiro.
- Os registros devem permitir auditoria sem expor chaves, assinaturas ou dados
  financeiros completos.
- A futura implementação ou pseudocódigo pertence ao Pacote D e não faz parte
  desta seção.

---

### 4.2.3 Implementação e Referência OWASP

Para mitigar o risco **R04 — Forjamento de notificações de callback de
pagamento** e atender ao requisito **RS03**, foi projetada a lógica de
verificação do endpoint `POST /api/v1/payments/callback`. A implementação
segue a decisão de arquitetura **DA02**, que estabelece o tratamento do callback
como notificação, e não como autorização de liberação.

#### A. Referência Oficial OWASP Utilizada

A lógica foi estruturada a partir da **OWASP Third Party Payment Gateway
Integration Cheat Sheet**, complementada pela **OWASP Cryptographic Storage
Cheat Sheet** quanto à comparação de valores criptográficos e pela **OWASP
Logging Cheat Sheet** quanto ao registro de eventos sem exposição de dados
sensíveis. As orientações aplicadas garantem que:

1. A notificação recebida do gateway é autenticada na origem antes de produzir
   qualquer efeito, por meio da verificação da assinatura **HMAC-SHA256**.
2. A comparação da assinatura utiliza **função de tempo constante**, e não o
   operador de igualdade comum, evitando que a diferença de tempo de resposta
   revele progressivamente o valor esperado.
3. O status do pagamento é confirmado por **consulta reversa servidor a
   servidor** à API oficial do gateway, de modo que a liberação nunca dependa
   exclusivamente do que a mensagem recebida afirma.
4. O valor, a moeda e o pedido informados são reconferidos contra os registros
   locais, e não aceitos como enviados.
5. O processamento é **idempotente**, evitando que o reenvio legítimo de um
   callback produza dupla liberação ou duplo registro financeiro.

#### B. Comportamento do Código e Lógica de Negócio

A verificação é organizada em seis etapas sequenciais, todas executadas **antes**
de qualquer alteração de estado. A ordem é deliberada: as verificações mais
baratas e mais decisivas vêm primeiro, de modo que uma requisição forjada seja
descartada sem consumir consulta ao banco nem chamada externa.

1. **Presença e formato da assinatura.** Ausência do cabeçalho de assinatura ou
   do identificador de transação encerra o processamento com `401`. Nenhuma
   consulta é realizada.
2. **Verificação da assinatura HMAC-SHA256.** O servidor recalcula o HMAC sobre
   o corpo bruto da requisição usando a chave secreta compartilhada e compara o
   resultado com o valor recebido por função de tempo constante. O corpo é lido
   em sua forma original, pois qualquer reserialização alteraria o hash.
3. **Proteção contra reprocessamento (idempotência).** Se o identificador de
   transação já constar como processado, o servidor responde `200` confirmando
   o estado atual, **sem** repetir a liberação. O reenvio é comportamento
   normal de gateways e não deve ser tratado como ataque.
4. **Existência e estado do pedido.** O pedido é localizado e precisa estar
   pendente. Pedido inexistente, cancelado ou já pago não avança.
5. **Correspondência de valor e moeda.** Valor e moeda informados são comparados
   com os registros locais do pedido. Divergência encerra o processamento e
   trata também a ameaça T06, em que o preço é adulterado no cliente.
6. **Confirmação reversa junto ao gateway.** A API consulta a transação
   diretamente na API oficial do provedor. Somente se o status retornado for de
   pagamento aprovado é que o pedido transita para pago e o curso associado é
   liberado, em operação única.

Todas as rejeições são registradas com horário, pedido, origem e motivo
sanitizado. **A chave secreta, a assinatura recebida e os dados financeiros
completos nunca são gravados em log**, conforme o critério de auditoria definido
na Seção 4.2.2.

#### C. Pseudocódigo da Implementação (Lógica do Servidor)

```
// ALGORITMO: Validação Segura de Callback de Pagamento (RS03 / DA02)

// ENDPOINT: Recebimento da notificação do gateway de pagamento
FUNCAO post_payments_callback(requisicao)

    // O corpo bruto é necessário: reserializar o JSON alteraria o hash calculado
    corpo_bruto        = requisicao.corpo_bruto
    assinatura_recebida = requisicao.cabecalhos["X-Gateway-Signature"]
    origem              = requisicao.ip

    dados = interpretar_json(corpo_bruto)

    // ETAPA 1: Presença dos elementos obrigatórios
    SE assinatura_recebida VAZIA OU dados.id_transacao VAZIO ENTAO
        registrar_evento_seguranca("callback_sem_assinatura", origem, dados.id_pedido)
        RETORNAR STATUS_HTTP(401, "Requisição não autenticada.")
    FIM_SE

    // ETAPA 2: Verificação criptográfica da origem e da integridade
    assinatura_calculada = hmac_sha256(corpo_bruto, obter_chave_secreta_gateway())

    // Comparação em tempo constante: evita vazamento por análise de tempo de resposta
    SE comparar_tempo_constante(assinatura_calculada, assinatura_recebida) == FALSO ENTAO
        registrar_evento_seguranca("callback_hmac_invalido", origem, dados.id_pedido)
        RETORNAR STATUS_HTTP(401, "Requisição não autenticada.")
    FIM_SE

    // ETAPA 3: Idempotência — reenvio legítimo não pode duplicar a liberação
    SE transacao_ja_processada(dados.id_transacao) ENTAO
        registrar_evento("callback_reenviado", dados.id_transacao)
        RETORNAR STATUS_HTTP(200, "Notificação já processada anteriormente.")
    FIM_SE

    // ETAPA 4: Existência e estado do pedido
    pedido = buscar_pedido_por_id(dados.id_pedido)

    SE pedido == NULO OU pedido.status != "PENDENTE" ENTAO
        registrar_evento_seguranca("callback_pedido_invalido", origem, dados.id_pedido)
        RETORNAR STATUS_HTTP(409, "Pedido inexistente ou já finalizado.")
    FIM_SE

    // ETAPA 5: Correspondência de valor e moeda (trata também T06)
    SE dados.valor != pedido.valor OU dados.moeda != pedido.moeda ENTAO
        registrar_evento_seguranca("callback_valor_divergente", origem, pedido.id)
        RETORNAR STATUS_HTTP(409, "Divergência entre a notificação e o pedido.")
    FIM_SE

    // ETAPA 6: Confirmação reversa servidor a servidor com o gateway oficial
    status_real = consultar_transacao_no_gateway(dados.id_transacao)

    SE status_real != "APROVADO" ENTAO
        registrar_evento_seguranca("callback_nao_confirmado", origem, pedido.id)
        RETORNAR STATUS_HTTP(409, "Pagamento não confirmado pelo provedor.")
    FIM_SE

    // Todas as verificações aprovadas: transição única e atômica de estado
    INICIAR_TRANSACAO_BANCO
        marcar_transacao_como_processada(dados.id_transacao)
        pedido.status = "PAGO"
        salvar_pedido(pedido)
        liberar_matricula(pedido.id_aluno, pedido.id_curso)
    CONFIRMAR_TRANSACAO_BANCO

    // O log registra o desfecho, nunca a chave secreta nem a assinatura
    registrar_evento("pagamento_confirmado", pedido.id, dados.id_transacao)

    RETORNAR STATUS_HTTP(200, "Pagamento processado com sucesso.")
FIM_FUNCAO
 // =============================================================================
// PRÁTICA DE CÓDIGO SEGURO 2 — VALIDAÇÃO DE CALLBACK DE PAGAMENTO
// Projeto Nexora — Engenharia de Software Seguro — Grupo 19
// -----------------------------------------------------------------------------
// Risco tratado ........: R04 — Forjamento de notificações de callback
// Requisito atendido ...: RS03 — Verificação HMAC-SHA256 antes da liberação
// Decisão de arquitetura: DA02 — Callback como notificação, não autorização
// Ameaças de origem ....: T04, T06
// Casos de teste .......: TS03 (válido) e TS04 (malicioso) — Seção 4.2.1
// Referência OWASP .....: Third Party Payment Gateway Integration Cheat Sheet
// Autor ................: Erik Bruckmann Soares
// =============================================================================

```
#### D. Resultado Seguro Esperado

* **Rejeição de notificações forjadas (TS04):** um agente que descubra o
  endereço do endpoint e envie uma notificação de aprovação não obtém efeito
  algum. Sem a chave secreta compartilhada, a assinatura não pode ser
  reproduzida, e a requisição é encerrada com `401` na Etapa 2, antes de
  qualquer consulta ao banco ou liberação de conteúdo.
* **Defesa em profundidade sobre a chave HMAC:** ainda que a chave secreta
  vazasse, permitindo forjar uma assinatura válida, a Etapa 6 impediria a
  liberação, pois a confirmação é obtida por canal iniciado pela própria
  Nexora. Nenhuma garantia depende de um único segredo.
* **Bloqueio da adulteração de valor (T06):** uma notificação assinada, porém
  com valor inferior ao do pedido registrado, é recusada na Etapa 5. O preço
  aceito é sempre o do servidor, nunca o informado na requisição.
* **Reenvio legítimo tratado sem efeito colateral (TS03):** quando o gateway
  reenvia um callback já processado, a Etapa 3 responde confirmando o estado
  atual sem repetir a liberação nem gerar novo registro financeiro,
  comprovando a transição única exigida pelos critérios de aprovação.
* **Rastreabilidade sem exposição de dados sensíveis:** cada rejeição fica
  registrada com pedido, origem, horário e motivo, permitindo auditoria e
  alimentando a regra de detecção **RD02** da Etapa 6. A chave secreta, a
  assinatura recebida e os dados financeiros completos permanecem fora dos
  registros.

 
