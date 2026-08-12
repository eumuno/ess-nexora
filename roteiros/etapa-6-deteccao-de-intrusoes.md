# 6 — Monitoramento e Detecção de Intrusões

Este roteiro organiza controles de detecção para os riscos priorizados da Nexora.

---

## 6.1 Fundamentação de Detecção de Intrusões

### O que é Detecção de Intrusões?
A detecção de intrusões consiste no processo de monitoramento contínuo de um ambiente computacional (seja no nível de rede, hosts ou no nível de aplicação) com o objetivo de identificar comportamentos anômalos, desvios de políticas de uso ou sinais de atividades maliciosas em andamento. Em uma aplicação web como a Nexora, o foco principal é a detecção no nível da aplicação, analisando requisições, logs de auditoria e fluxos transacionais para identificar abusos lógicos e tentativas de ataque que simulam tráfego legítimo.

### Diferença Técnica: Prevenir vs. Detectar
No contexto de Engenharia de Software Seguro e com base nas melhores práticas do NIST Cybersecurity Framework, as ações preventivas e as ações detectivas atuam em camadas complementares de defesa:

1. **Prevenir (Função Protect):** É uma abordagem ativa e restritiva. Consiste em estabelecer barreiras técnicas, controles e salvaguardas com o intuito de impedir que uma ameaça se concretize ou explore uma vulnerabilidade existente. O objetivo é bloquear o incidente na origem antes que ocorra dano. Exemplos: controle de acesso multifator (MFA), criptografia em trânsito e em repouso,Prepared Statements para injeção de código, sanitização de entrada e mecanismos de limitação de taxa (Rate Limiting).
2. **Detectar (Função Detect):** É uma abordagem de observabilidade e vigilância. Assume-se a premissa de que nenhuma barreira preventiva é 100% infalível e de que incidentes inevitavelmente ocorrerão. A detecção foca na análise de logs, monitoramento de métricas de desempenho e disparo de alertas em tempo real ao identificar anomalias lógicas, bypasses de controles ou atividades suspeitas. O objetivo é dar visibilidade imediata ao evento para mitigar o impacto. Exemplos: logs de auditoria detalhados, alarmes de uso anormal de recursos, correlação de logs de segurança e sistemas de monitoramento comportamental.

Em resumo, a prevenção atua de forma proativa para **evitar o incidente**, enquanto a detecção atua de forma reativa/contínua para **identificar o incidente em andamento** e acionar as defesas de contenção.

---

## 6.2 Eventos de Log Necessários na Nexora

Para sustentar as regras de monitoramento, o backend da plataforma Nexora deve alimentar uma trilha de auditoria centralizada, protegida e protegida contra exclusão intencional, permitindo a investigação completa de incidentes.

### 6.2.1 Eventos Específicos que Devem ser Registrados
Os eventos críticos mapeados para o ecossistema da Nexora são categorizados em seis grupos de interesse:

1. **Tentativas de Autenticação e Gestão de Credenciais (Sessão):**
   - Tentativas de login bem-sucedidas e malsucedidas (especialmente falhas sucessivas).
   - Bloqueio de contas por excesso de tentativas (Rate Limiting acionado).
   - Solicitações de alteração de senhas ou reset de chaves de recuperação.
   - Ativação ou desativação de dispositivos confiáveis e autenticação de múltiplos fatores (MFA).
   - Desconexões forçadas (logout) e expiração de sessões JWT.

2. **Alterações Administrativas e de Privilégios (Acesso):**
   - Criação, atualização ou exclusão de contas com perfis privilegiados (Administradores, Moderadores, Equipe Financeira).
   - Elevação de privilégios de qualquer usuário (ex: um Aluno promovido a Instrutor).
   - Consultas de leitura ou exportação de logs brutos executadas por usuários do suporte.

3. **Operações Financeiras Sensíveis (Faturamento):**
   - Alteração ou inclusão de dados de recebimento financeiro (chaves Pix e contas bancárias de instrutores).
   - Processamento de transações, estornos parciais ou totais e falhas de pagamento enviadas pelo gateway integrado.
   - Liberação de assinaturas ou cursos efetuadas de forma manual pelo suporte financeiro.

4. **Operações Acadêmicas e Publicação de Conteúdo:**
   - Criação, edição e exclusão de videoaulas, cursos ou materiais complementares (PDFs) por instrutores.
   - Alteração manual de notas de avaliações acadêmicas, progresso de alunos ou redefinição de trilhas por moderadores.
   - Solicitação e emissão de certificados digitais de conclusão de curso.

5. **Interação Social e Moderação (Fóruns):**
   - Moderações ativas (exclusão de comentários, remoção de posts ofensivos e aplicação de banimentos).
   - Requisições sequenciais excessivas em perfis de usuários no fórum (padrão indicativo de raspagem massiva ou IDOR).

6. **Eventos do Servidor de Banco de Dados e Sistema:**
   - Detecção de payloads maliciosos ou falhas de validação de entrada nas APIs de backend.
   - Erros do lado do servidor (status HTTP 500) gerados em rotas sensíveis de dados.

### 6.2.2 Atributos e Estrutura dos Registros de Logs (Metadados)
Cada entrada registrada no sistema de log de auditoria deve ser encapsulada em um formato estruturado (JSON) e possuir o seguinte esquema de dados obrigatório para viabilizar auditorias técnicas:

* **id_log:** Hash criptográfico sequencial único do registro para garantir integridade.
* **timestamp:** Data e hora exata da ocorrência, sincronizada centralmente via protocolo de tempo NTP (Network Time Protocol) para evitar divergências.
* **id_operador:** Identificador único (User ID) do usuário que realizou a ação (ou "Anônimo" para fluxos de visitantes).
* **perfil_operador:** Papel do usuário logado no momento do evento (Aluno, Instrutor, Administrador, etc.).
* **tipo_evento:** Tag taxonômica específica do evento para facilitar a filtragem (ex: AUTH_LOGIN_FAIL, FIN_BANK_UPDATE, ACAD_CERT_GENERATE).
* **ip_origem:** Endereço IP do cliente que submeteu a requisição que originou o registro.
* **agente_usuario:** String do User-Agent descrevendo o navegador e o sistema operacional de origem.
* **recurso_afetado:** Identificador único do elemento que sofreu a ação (ex: id_curso, id_aluno, id_certificado).
* **estado_anterior:** Hash ou objeto representativo dos valores dos dados antes de sofrerem alteração (para modificações sensíveis).
* **estado_posterior:** Hash ou objeto representativo dos valores gravados após a modificação.
* **resultado:** Indicativo booleano de sucesso ou falha na tentativa de execução da operação.
* **justificativa_operador:** Texto explicativo obrigatório inserido por usuários em rotas de moderação ou faturamento crítico.

---

## 6.3 Regras de Detecção (Regras 1 e 2)

Os limiares são valores iniciais de projeto e deverão ser calibrados com dados reais de uso para reduzir falsos positivos.

| Regra | Risco observado | Fonte de dados | Condição objetiva de alerta | Resposta inicial |
| :---: | :--- | :--- | :--- | :--- |
| **RD01** | **R01 / T01 / CA03 — credential stuffing e sequestro de conta** | Logs de autenticação e rate limiting: horário, IP, conta, resultado, perfil e sessão; sem senhas ou códigos MFA. | Alerta alto quando um IP falhar em **5 ou mais contas distintas em 10 minutos**, ou quando Instrutor/Admin tiver **5 falhas consecutivas em 15 minutos**. | Limitar IP e conta temporariamente conforme RS01, preservar eventos, notificar segurança e avisar o titular de conta privilegiada. |
| **RD02** | **R04 / T04 — callback de pagamento forjado** | Logs do callback, resultado HMAC, pedido, transação e origem; sem chave secreta ou dados financeiros completos. | Alerta alto para callback com HMAC inválido de pedido existente ou **3 rejeições** da mesma origem/pedido em **5 minutos**. | Não liberar curso, manter pedido no estado anterior, limitar origem quando repetido, preservar evidência e confirmar o status no gateway. |

### 6.3.1 Justificativas e limitações

RD01 detecta tentativas distribuídas entre contas, não apenas falhas de um único usuário. NAT, proxies e esquecimento de senha podem gerar falso positivo; a contenção inicial é temporária e deve correlacionar IP, conta, dispositivo e histórico.

RD02 trata uma assinatura inválida como relevante, mas rotação de chaves, relógios dessincronizados ou erro de integração também podem gerar alerta. A liberação exige confirmação servidor a servidor; chave HMAC, assinatura completa e dados sensíveis não devem ser registrados.

---

## 6.4 Regra de Detecção (Regra 3)

A terceira regra de detecção foi projetada especificamente para mitigar o risco de **Exposição de Informações Cadastrais (R07)** decorrente de vulnerabilidades de controle de acesso a nível de objeto, como a descrita no caso de abuso **CA04 — Visualização Insegura de Perfis de Outros Alunos (IDOR)**.

| Campo | Descrição |
| :--- | :--- |
| **Regra** | **RD03 — Tentativa de Varredura ou Raspagem de Dados de Perfis (IDOR / Data Scraping)** |
| **Risco observado** | R15 / T15 / CA04 — Vazamento de informações e exposição indevida de dados pessoais de alunos (como CPF e e-mail) por manipulação de parâmetros. |
| **Fonte de dados** | Logs de requisições HTTP do API Gateway e do Servidor Web (direcionados ao endpoint `/api/v1/users/perfil`), contendo ID do usuário autenticado, IP de origem, cabeçalhos de requisição e status de retorno HTTP. |
| **Condição de alerta** | Alerta de **prioridade alta** quando uma mesma credencial de usuário autenticada (ou um mesmo IP não autenticado de Visitante) realizar requisições para **mais de 30 IDs de perfis diferentes em um intervalo menor que 2 minutos**. |
| **Resposta inicial** | Revogação imediata do token de sessão ativo (JWT) do usuário requisitante, bloqueio temporário do IP de origem no API Gateway por 1 hora e registro do evento no servidor de auditoria para investigação detalhada. |

### 6.4.1 Justificativa e Limitações da RD03

A regra RD03 é essencial para detectar comportamentos automatizados de raspagem de dados (*scraping*) antes que ocorra a extração em massa da base de dados de estudantes da Nexora. Como limitação operacional, comportamentos legítimos de navegação rápida pelas páginas do fórum de dúvidas ou perfis de instrutores podem gerar potenciais falsos positivos. O limiar de 30 requisições em 2 minutos é dimensionado para capturar apenas requisições em velocidades e volumes característicos de ferramentas automatizadas, minimizando o impacto sobre a navegação humana comum.

---

## 6.5 Roteiro de Resposta pós-Alerta

Este roteiro estabelece o plano conceitual de resposta a incidentes de segurança da plataforma Nexora. Assim que uma das regras de detecção de intrusões (**RD01, RD02 ou RD03**) for disparada, a equipe técnica e administrativa deverá seguir estritamente as quatro fases descritas a seguir:

### 1. Notificação e Triagem (Minutos 0 a 15)
*   **Notificação Automatizada:** O acionamento de qualquer regra de detecção gera um log centralizado de alta prioridade. O sistema dispara imediatamente um alerta contendo o ID do alerta, IP de origem, contas afetadas e dados técnicos sanitizados para o canal de comunicação interna da equipe de segurança.
*   **Triagem:** O Administrador de plantão realiza a análise rápida para verificar a legitimidade do alerta, se houve manutenção no gateway de pagamento, testes autorizados de carga ou se é de fato um comportamento anômalo.

### 2. Contenção e Bloqueio Temporário (Minutos 15 a 30)
*   **Contenção no API Gateway:** Conforme especificado nas regras de detecção, o API Gateway valida e executa o bloqueio do IP do atacante ou revoga as sessões comprometidas temporariamente.
*   **Bloqueio de Contas Privilegiadas (RD01):** Se o login suspeito for associado a uma conta de **Instrutor** ou **Administrador**, a conta correspondente é suspensa preventivamente para evitar a alteração de dados de cursos ou dados bancários de repasse.
*   **Prevenção de Entrega de Conteúdo (RD02):** Caso o alerta envolva callbacks financeiros forjados, o sistema congela a liberação automática do curso pendente no banco de dados e notifica a equipe financeira para realizar a conciliação bancária manual direta com a API do gateway (Stripe ou Iugu).

### 3. Erradicação e Correção (Horas 1 a 24)
*   **Redefinição Forçada de Credenciais:** No caso de contas comprometidas por *credential stuffing* (RD01), exige-se uma alteração mandatória de senha baseada em regras de complexidade forte e uma revalidação dos fatores de autenticação multifator (MFA) do usuário.
*   **Sanitização de Registros de Banco de Dados:** Quaisquer alterações indevidas feitas em registros de matrículas, dados de repasse ou certificados durante a intrusão são desfeitas utilizando backups lógicos seguros e auditoria reversa de logs.

### 4. Recuperação, Pós-Incidente e Notificação Legal (Após 24 horas)
*   **Restabelecimento do Acesso:** As contas suspensas são liberadas apenas após a confirmação manual de identidade do usuário pela equipe de moderação e suporte.
*   **Comunicação aos Usuários e LGPD:** Se for constatado que houve vazamento efetivo de dados pessoais de alunos (RD03/CA04), a equipe jurídica e de segurança da Nexora notificará os indivíduos afetados e as autoridades reguladoras competentes no prazo previsto em lei, detalhando os dados expostos e as ações corretivas tomadas.
*   **Lições Aprendidas:** A equipe técnica revisa as regras de detecção no sistema de monitoramento para ajustar limiares de bloqueio e atualizar as políticas de segurança de desenvolvimento do código.

---

## 6.6 Setup e Estrutura do Roteiro

Esta seção consolida a organização do roteiro, registra a rastreabilidade entre
as regras de detecção e as etapas anteriores e verifica a conformidade do
documento com o entregável mínimo exigido.

### 6.6.1 Estrutura do documento

O roteiro está organizado em uma sequência que parte do conceito e chega à
resposta operacional:

| Seção | Conteúdo | Função no roteiro |
| :---: | :--- | :--- |
| **6.1** | Fundamentação de detecção de intrusões | Estabelece o conceito e a distinção entre prevenir e detectar |
| **6.2** | Eventos de log necessários | Define a matéria-prima que alimenta as regras |
| **6.3** | Regras de detecção RD01 e RD02 | Trata os riscos de autenticação e de pagamento |
| **6.4** | Regra de detecção RD03 | Trata o risco de exposição de dados pessoais |
| **6.5** | Roteiro de resposta pós-alerta | Define o que ocorre depois que uma regra dispara |
| **6.6** | Setup e estrutura do roteiro | Consolida a rastreabilidade e verifica a conformidade |

A ordem não é arbitrária. Cada seção fornece o insumo da seguinte: sem os
eventos definidos na 6.2, as condições de alerta da 6.3 e da 6.4 não teriam
fonte de dados; sem as regras, o roteiro de resposta da 6.5 não teria gatilho.

### 6.6.2 Escopo e premissas adotadas

O roteiro descreve **detecção no nível da aplicação**, e não no nível de rede.
Essa escolha decorre da natureza das ameaças identificadas na Etapa 1: a maior
parte dos abusos da Nexora ocorre por meio de requisições formalmente válidas,
originadas de usuários autenticados, que um sistema de detecção baseado em
assinaturas de tráfego não distinguiria do uso legítimo.

Conforme o enunciado da etapa, **nenhum sistema de detecção foi instalado ou
implementado**. As regras são especificações de projeto, e os limiares
apresentados são valores iniciais que deverão ser calibrados com dados reais de
operação antes de qualquer aplicação em produção.

Os registros descritos na Seção 6.2 seguem o princípio da minimização: senhas,
códigos de segundo fator, chaves HMAC, assinaturas completas e dados
financeiros integrais **não são registrados em nenhuma hipótese**, conforme já
estabelecido nas regras RD01 e RD02.

### 6.6.3 Rastreabilidade das regras de detecção

| Regra | Ameaça (Etapa 1) | Risco (Etapa 2) | Caso de abuso | Requisito ou decisão relacionada |
| :---: | :---: | :---: | :---: | :--- |
| **RD01** | T01 | R01 | CA03 | RS01 e DA01 — MFA e limitação de tentativas |
| **RD02** | T04 | R04 | — | RS03 e DA02 — validação HMAC do callback |
| **RD03** | T15 | R15 | CA04 | Autorização em nível de objeto na API |

A tabela evidencia que nenhuma regra foi criada de forma isolada: cada uma
deriva de uma ameaça modelada na Etapa 1, avaliada como risco na Etapa 2 e, nos
casos de RD01 e RD02, já tratada por um requisito e uma decisão de arquitetura
nas Etapas 3 e 4.

Essa correspondência sustenta a complementaridade descrita na Seção 6.1: RD01 e
RD02 observam justamente os pontos em que os controles preventivos poderiam
falhar ou ser contornados, enquanto RD03 cobre um risco cujo tratamento depende
de verificação de autorização a cada requisição, condição em que o
monitoramento comportamental é especialmente relevante.

### 6.6.4 Cobertura dos riscos e limitações do escopo

As três regras cobrem os riscos de maior prioridade em três frentes distintas —
identidade, integridade financeira e confidencialidade dos dados pessoais. A
escolha atende ao número mínimo previsto no enunciado e evita concentrar a
detecção em uma única categoria de ameaça.

Assume-se, contudo, que **três regras não cobrem todo o registro de riscos da
Etapa 2**. Permanecem sem regra dedicada, entre outros, os riscos associados à
adulteração de registros acadêmicos, à falsificação de certificados e à
indisponibilidade da plataforma. Esses riscos continuam tratados por controles
preventivos das Etapas 3 e 4, e a ampliação do conjunto de regras é um
desdobramento natural do trabalho, condicionado à calibração das três primeiras
com dados reais.

### 6.6.5 Conformidade com o entregável mínimo

| Item exigido pelo enunciado | Seção correspondente | Situação |
| :--- | :---: | :---: |
| Explicar o que é detecção de intrusões | 6.1 | ✅ |
| Explicar a diferença entre prevenir e detectar | 6.1 | ✅ |
| Indicar quais eventos deveriam ser registrados | 6.2 | ✅ |
| Apresentar três regras simples de detecção | 6.3 e 6.4 | ✅ |
| Definir o que acontece depois de um alerta | 6.5 | ✅ |
| Campos obrigatórios de cada regra: risco observado, fonte de dados, condição de alerta e resposta inicial | 6.3 e 6.4 | ✅ |

As três regras apresentam os quatro campos exigidos, e cada uma acompanha uma
subseção de justificativa e limitações — 6.3.1 e 6.4.1 — que registra as
condições capazes de gerar falso positivo. O documento está armazenado no
caminho previsto, `roteiros/etapa-6-deteccao-de-intrusoes.md`.
