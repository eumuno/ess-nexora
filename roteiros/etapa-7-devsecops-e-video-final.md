# 7 — Pipeline DevSecOps e Apresentação em Vídeo

---

## 7.1 Atividades de Segurança no Pipeline DevSecOps

Para garantir que a segurança não seja um componente adicionado tardiamente ao software, a plataforma Nexora adota a filosofia DevSecOps. O ciclo de vida do desenvolvimento de software (SDLC) foi reestruturado para incorporar controles, verificações e validações de segurança automatizadas e manuais em cada uma de suas oito fases principais.

A tabela a seguir mapeia cada momento do pipeline, as atividades de segurança correspondentes, as evidências geradas (conectando com o trabalho prático realizado pelo grupo nas etapas anteriores) e os critérios mínimos de continuidade para que o pipeline avance de forma segura.

### Tabela Mestra de Atividades de Segurança do Pipeline

| Momento / Fase do SDLC | Atividade de Segurança Realizada | Evidência Técnica Produzida | Condição Mínima de Continuidade |
| :--- | :--- | :--- | :--- |
| **1. Planejamento** | Modelagem de ameaças e mapeamento dos principais Casos de Abuso relacionados ao negócio. | Documento Markdown de análise STRIDE contendo as ameaças e comportamentos de ataque modelados. | 100% dos ativos críticos identificados e ameaças de alto impacto mapeadas pelo grupo. |
| **2. Requisitos e Arquitetura** | Definição de requisitos de segurança baseados no NIST CSF 2.0 e tomada de decisões arquiteturais seguras. | Registro de riscos priorizados, requisitos de segurança formais e diagrama da arquitetura segura. | Plano de tratamento de riscos aprovado e fronteiras de confiança da arquitetura claramente estabelecidas. |
| **3. Implementação (Código)** | Desenvolvimento defensivo baseado em guias oficiais (OWASP Cheat Sheets) para mitigar falhas comuns. | Código-fonte ou pseudocódigo estruturado contendo as práticas defensivas implementadas. | Zero credenciais, chaves de API ou segredos de ambiente expostos em texto simples no código-fonte. |
| **4. Testes** | Criação e execução de planos de testes funcionais e testes de segurança focados nos fluxos críticos. | Especificações técnicas de casos de teste de segurança positivos e negativos documentados. | 100% dos testes de regressão de segurança (ex: validação de token MFA e bloqueio por força bruta) aprovados. |
| **5. Análise de Código (SAST/SCA)** | Varredura estática de vulnerabilidades no código (SAST) e análise de vulnerabilidades em bibliotecas de terceiros (SCA). | Relatórios automatizados gerados por ferramentas de varredura estática e análise de dependências desatualizadas. | Ausência de dependências de terceiros com vulnerabilidades conhecidas (CVEs) críticas sem mitigação ativa. |
| **6. Verificação Dinâmica (DAST)** | Varredura de segurança dinâmica ativa e passiva simulando ataques em ambiente controlado de homologação. | Relatório consolidado contendo os alertas de segurança gerados pela ferramenta de varredura ativa. | Mitigação e correção técnica validada de todos os alertas classificados com severidade Crítica ou Alta. |
| **7. Implantação (Deployment)** | Provisionamento seguro de servidores, hardening de contêineres e deploy automatizado via CI/CD. | Scripts de automação de infraestrutura revisados e certificados criptográficos SSL/TLS configurados. | Build de produção gerado com sucesso sem avisos de configuração incorreta ou portas administrativas expostas. |
| **8. Operação e Monitoramento** | Registro contínuo de logs de auditoria estruturados e aplicação de regras de correlação de alertas. | Trilha de auditoria em JSON centralizada e roteiro de resposta rápida pós-alertas de segurança. | Metadados de logs obrigatórios integrados de forma inviolável e servidores de logs protegidos contra exclusão. |

---

## 7.2 Condições de Bloqueio (Gates de Segurança)

| Gate | Fase e bloqueio | Evidência | Análise e liberação |
| :---: | :--- | :--- | :--- |
| **G01** | **Testes:** falha em teste obrigatório de autenticação, autorização ou callback (TS03/TS04). | Relatório com teste, entrada, resultado esperado e obtido. | **Desenvolvimento e responsável técnico.** Corrigir, repetir a suíte afetada e anexar aprovação. |
| **G02** | **SAST/SCA/DAST:** vulnerabilidade crítica, ou alta explorável, sem análise e tratamento. | Relatório do scanner e triagem com severidade e alcance. | **Segurança, desenvolvimento e responsável pelo componente.** Corrigir/mitigar e revalidar; exceção exige justificativa, prazo e controle compensatório. |
| **G03** | **Commit/CI:** scanner identifica segredo válido no repositório, artefato ou log de build. | Relatório mascarado do scanner. | **Desenvolvimento e infraestrutura.** Remover o segredo, revogar/rotacionar a credencial e repetir a varredura. |
| **G04** | **Entrega:** artefato obrigatório sem evidência verificável. | Checklist e validação de caminhos. | **Responsável da etapa e coordenação.** Adicionar/corrigir, validar e revisar o artefato. |

Os gates exigem evidência, responsável e decisão registrada; uma exceção não pode apenas ignorar um alerta.

---

## 7.3 Considerações Finais e Aprendizados do Grupo

STRIDE e os casos de abuso transformaram fluxos de autenticação, pagamentos, conteúdo e administração em riscos comparáveis. A priorização orienta requisitos verificáveis, como MFA, limitação de tentativas e validação de callbacks, que por sua vez permitem definir testes antes da implementação.

Testes, verificação de vulnerabilidades e revisão humana são complementares: testes validam comportamentos conhecidos; ferramentas revelam achados a triar; e evidências reais são necessárias para afirmar vulnerabilidades ou eficácia de controles. Logs minimizados e regras de detecção apoiam resposta a falhas preventivas sem expor dados sensíveis.

O DevSecOps conecta essa rastreabilidade aos gates de entrega. Limitações atuais incluem ausência de implementação completa e de resultados reais de verificação; melhorias futuras incluem automatizar validações, calibrar regras com métricas reais e revisar periodicamente riscos e controles.

---

## 7.4 Roteiro e Script do Vídeo Final de Apresentação

Esta seção define o roteiro da apresentação em vídeo, especificando a ordem dos
blocos, o responsável por cada fala, o tempo previsto, a tela demonstrada e o
texto sugerido para a narração.

### 7.4.1 Parâmetros da apresentação

| Item | Definição |
| :--- | :--- |
| **Duração-alvo** | 7 minutos, dentro da faixa de 5 a 8 minutos prevista no enunciado |
| **Formato** | Captura de tela individual com áudio, unificada em linha do tempo única |
| **Recurso em tela** | Repositório `eumuno/ess-nexora` aberto no navegador, com os arquivos e diagramas exibidos conforme cada bloco |
| **Ordem de apresentação** | Segue a evolução cronológica do projeto, da Etapa 1 à Etapa 7 |
| **Participantes** | Os quatro integrantes, conforme a divisão registrada na Seção 7.5.2 |

### 7.4.2 Orientações gerais de gravação

O enunciado é explícito ao dispensar a demonstração de todas as tabelas. Cada
bloco deve **mostrar uma tela e explicar a decisão que ela representa**, e não
percorrer o conteúdo linha a linha. A tela existe para dar concretude à fala,
não para ser lida em voz alta.

Cada integrante grava sua parte separadamente, com o arquivo correspondente já
aberto e posicionado na seção que será citada, evitando rolagem prolongada em
tela. Recomenda-se gravar com o repositório em tela cheia e ampliar a fonte do
navegador para que o conteúdo permaneça legível após a compressão do vídeo.

As transições entre blocos devem retomar o encadeamento do projeto: cada etapa
é apresentada como consequência da anterior, e não como item isolado de uma
lista. Esse encadeamento é o principal critério de avaliação da Etapa 7 —
integração entre as etapas — e deve ficar audível na narração.

**Sobre as falas sugeridas.** O texto de cada bloco é apoio à gravação, não
leitura obrigatória. Cada integrante deve adaptá-lo ao próprio modo de falar,
preservando os pontos técnicos e as frases de transição. Os trechos entre
colchetes indicam ações em tela e não devem ser lidos em voz alta.

---

### 7.4.3 Roteiro detalhado por bloco

#### Bloco 1 — Abertura e apresentação do sistema
**Responsável:** Bruna · **Duração:** 0:00 – 0:50 · **Tela:** `README.md` na raiz do repositório

**Direção.** Apresentar o grupo e a disciplina, introduzir a Nexora e justificar
a escolha do sistema. Exibir o guia de navegação do README para evidenciar que o
repositório acompanha as sete etapas.

**Fala sugerida.**

> Olá! Somos o Grupo 19 da disciplina de Engenharia de Software Seguro, formado
> por Bruna, Erik, Gabriela e Inaurrara. O sistema que analisamos ao longo do
> semestre é a Nexora, uma plataforma fictícia de ensino online que conecta
> alunos e instrutores independentes.
>
> Escolhemos essa plataforma porque ela reúne, em um único sistema, quase tudo
> o que torna a segurança difícil: múltiplos perfis de acesso com privilégios
> diferentes, processamento de pagamentos, propriedade intelectual de terceiros
> e dados pessoais protegidos pela LGPD.
>
> [mostrar o guia de navegação do README]
>
> O repositório acompanha as sete etapas do trabalho, e é isso que vamos
> percorrer agora: partimos das ameaças e chegamos até o pipeline que sustenta
> essas decisões ao longo do tempo.

---

#### Bloco 2A — Ameaças e casos de abuso
**Responsável:** Gabriela · **Duração:** 0:50 – 1:35 · **Tela:** `docs/etapa-1-ameacas-stride.md`, Seções 1.5 e 1.6

**Direção.** Explicar a mudança de perspectiva do STRIDE, rolar a tabela de
ameaças para mostrar a extensão do mapeamento — sem parar em linhas específicas
— e destacar dois casos de abuso concretos.

**Fala sugerida.**

> Na primeira etapa, mudamos de perspectiva: em vez de pensar no que o sistema
> deve fazer, pensamos no que alguém poderia fazer contra ele. Usamos o método
> STRIDE, que organiza as ameaças em seis categorias.
>
> [rolar a tabela da Seção 1.5]
>
> Todas as seis categorias se mostraram aplicáveis à Nexora — nenhuma foi
> descartada — justamente pela combinação de perfis, pagamentos e dados
> pessoais que a Bruna mencionou.
>
> Dessas ameaças, derivamos casos de abuso concretos. Destaco dois. O **CA03**
> é o sequestro de conta de um instrutor: o atacante entra com credenciais
> vazadas, troca a chave Pix de recebimento e desvia os repasses das vendas. E o
> **CA04**, em que um aluno troca o número do identificador na barra de endereços
> e passa a ver os dados cadastrais de outros alunos — CPF, e-mail, tudo.

---

#### Bloco 2B — Riscos prioritários
**Responsável:** Inaurrara · **Duração:** 1:35 – 2:20 · **Tela:** `docs/etapa-2-riscos-nist.md`, Seções 2.4 e 2.6

**Direção.** Mostrar a matriz de probabilidade e impacto, explicar o cálculo do
nível e apresentar a priorização final.

**Fala sugerida.**

> Identificar as ameaças, porém, não diz por onde começar. Foi o que fizemos na
> Etapa 2: transformamos cada ameaça em um risco avaliável.
>
> [mostrar a matriz de probabilidade e impacto]
>
> Cada risco recebeu uma nota de probabilidade e uma de impacto, de 1 a 4. A
> multiplicação das duas define o nível: baixo, médio, alto ou crítico. Mas a
> pontuação sozinha não decide nada — cada valor foi justificado pelo contexto
> real da plataforma.
>
> [mostrar a priorização da Seção 2.6]
>
> A priorização é o que define onde investir primeiro, já que nem todo risco
> exige o mesmo esforço. E é a partir daqui que o trabalho deixa de ser análise
> e passa a ser projeto.

---

#### Bloco 3 — Decisões de arquitetura
**Responsável:** Erik · **Duração:** 2:20 – 3:30 · **Telas:** `diagramas/etapa-3/arquitetura-segura.png` e `docs/etapa-3-arquitetura-segura.md`, Seção 3.4

**Direção.** Abrir com o diagrama, apontando o limite de confiança e a posição
dos controles. Apresentar as três decisões de forma sintética e encerrar
mostrando a tabela de rastreabilidade da 3.4.1.

**Fala sugerida.**

> Com os riscos priorizados, a pergunta passou a ser: onde, exatamente, colocar
> cada proteção?
>
> [abrir o diagrama de arquitetura segura]
>
> Esse é o diagrama da arquitetura segura da Nexora. A linha vermelha tracejada
> é o limite de confiança: tudo que vem de fora dela é tratado como não
> confiável. Os cadeados verdes marcam onde cada controle foi posicionado.
>
> Os requisitos RS01 a RS03 definiram *o que* garantir. As decisões DA01 a DA03
> definiram *como e onde*.
>
> A **DA01** centraliza a autenticação em um serviço dedicado e coloca a
> limitação de tentativas na borda. Essa separação é intencional: a borda
> protege o custo de processamento, descartando ataques automatizados antes de
> verificar senhas; e a centralização garante que exista um caminho único até um
> token válido, para que a exigência de MFA não possa ser contornada por outra
> interface.
>
> A **DA02** trata o callback de pagamento como notificação, e não como
> autorização. Mesmo com assinatura válida, a Nexora confirma o pagamento por
> uma consulta que ela mesma inicia — assim, nenhuma garantia depende de um
> único segredo.
>
> E a **DA03** segrega o domínio de envio de e-mails e substitui a solicitação de
> senha por links de uso único.
>
> [mostrar a tabela de rastreabilidade da 3.4.1]
>
> Cada decisão é rastreável: da ameaça ao risco, ao requisito, à decisão e à
> fraqueza catalogada na CWE. E cada uma registra o que foi descartado. Por
> exemplo: MFA para todos os perfis foi descartado por desproporcionalidade, e
> bloqueio permanente de conta foi descartado porque transformaria um controle
> de proteção em vetor de negação de serviço.

---

#### Bloco 4A — Prática de código seguro 1
**Responsável:** Bruna · **Duração:** 3:30 – 4:05 · **Tela:** `docs/etapa-4-codigo-seguro.md`, Seção 4.1

**Direção.** Enfatizar que os testes vieram antes da implementação. Mostrar o
pseudocódigo e destacar o fluxo em três fases.

**Fala sugerida.**

> Definida a arquitetura, partimos para o código. Foram duas práticas, e em
> ambas os casos de teste foram escritos **antes** da implementação.
>
> A primeira trata do risco R01, o uso indevido de conta por credenciais
> vazadas.
>
> [mostrar o pseudocódigo da Prática 1]
>
> O fluxo tem três fases. Primeiro o filtro de limitação, que bloqueia o IP após
> cinco tentativas inválidas em quinze minutos. Depois a validação da senha
> contra o hash. E aqui está o ponto central: se o perfil for de Instrutor ou
> Administrador, o sistema **não** emite a sessão — ele devolve um token
> temporário que só serve para a etapa do segundo fator. O token definitivo só
> sai depois do código de seis dígitos validado.

---

#### Bloco 4B — Prática de código seguro 2
**Responsável:** Erik · **Duração:** 4:05 – 4:40 · **Telas:** `docs/etapa-4-codigo-seguro.md`, Seção 4.2, e `codigo/etapa-4/`

**Direção.** Mostrar o pseudocódigo, explicar por que a ordem das verificações
importa e destacar os dois pontos técnicos centrais.

**Fala sugerida.**

> A segunda prática trata do risco R04, o forjamento de callbacks de pagamento.
>
> [mostrar o pseudocódigo da Prática 2]
>
> O endpoint de retorno do gateway é público por necessidade — qualquer um na
> internet pode enviar uma requisição para ele. Por isso são seis verificações,
> todas executadas antes de qualquer alteração de estado.
>
> A ordem importa: as verificações mais baratas vêm primeiro, para que uma
> requisição forjada seja descartada sem consumir consulta ao banco nem chamada
> externa.
>
> Dois pontos que valem destacar. A comparação da assinatura é feita em **tempo
> constante**, e não com o operador de igualdade comum — porque a diferença no
> tempo de resposta revelaria progressivamente o valor esperado. E o
> processamento é **idempotente** por identificador de transação: gateways
> reenviam callbacks quando não recebem confirmação, e sem isso um reenvio
> legítimo liberaria o curso duas vezes.

---

#### Bloco 5A — Ambiente de verificação
**Responsável:** Bruna · **Duração:** 4:40 – 5:05 · **Tela:** `evidencias/etapa-5/`, capturas 01 a 04

**Direção.** Explicar a limitação metodológica com franqueza e mostrar as
capturas do ambiente e da varredura.

**Fala sugerida.**

> Na Etapa 5, tivemos que lidar com uma limitação real: a Nexora é um projeto
> documental, não temos uma aplicação executável para varrer.
>
> A solução foi montar um ambiente de laboratório autorizado com o **OWASP Juice
> Shop**, uma aplicação deliberadamente vulnerável criada para treinamento.
>
> [mostrar as capturas do ambiente e da varredura]
>
> Rodamos o Juice Shop em container Docker na porta 3000 e executamos uma
> varredura ativa com o OWASP ZAP, restrita à instância local. Todas as
> evidências estão versionadas na pasta de evidências do repositório.

---

#### Bloco 5B — Resultados da verificação
**Responsável:** Gabriela · **Duração:** 5:05 – 5:40 · **Telas:** `05-zap-alertas-gerados.png` e `06-efeito-do-ataque-no-juiceshop.png`

**Direção.** Apresentar os três achados analisados, com destaque para o A01, e
encerrar com a ressalva metodológica registrada no relatório.

**Fala sugerida.**

> A varredura gerou vinte e três alertas. Analisamos três em profundidade.
>
> O **A01** é uma Injeção SQL. Enviando um payload no campo de login, o servidor
> devolveu um token de administrador válido — sem senha. É o bypass completo da
> autenticação, classificado como CWE-89 e A03 do OWASP Top 10.
>
> O **A02** é um Open Redirect, e não ficou só no alerta: o próprio Juice Shop
> registrou que o desafio de contornar a lista de destinos permitidos foi
> resolvido durante a varredura.
>
> E o **A03** é o identificador de sessão trafegando na URL. Esses dois se
> reforçam: o redirecionamento induz a saída para um domínio externo, e a sessão
> na URL faz essa saída carregar o identificador junto.
>
> Uma ressalva importante: esses achados comprovam falhas do ambiente de
> laboratório. A análise projeta o que aconteceria se condições equivalentes
> existissem na Nexora.

---

#### Bloco 6A — Regras de detecção RD01 e RD02
**Responsável:** Inaurrara · **Duração:** 5:40 – 6:05 · **Tela:** `roteiros/etapa-6-deteccao-de-intrusoes.md`, Seção 6.3

**Direção.** Estabelecer a distinção entre prevenir e detectar antes de
apresentar as regras, e explicitar que elas vigiam os controles das etapas
anteriores.

**Fala sugerida.**

> Até aqui, tudo que apresentamos é prevenção. Mas nenhuma barreira é infalível,
> e é por isso que a Etapa 6 trata de detecção.
>
> Prevenir é impedir que o incidente aconteça. Detectar é assumir que ele pode
> acontecer mesmo assim e garantir que alguém perceba.
>
> [mostrar as regras RD01 e RD02]
>
> A **RD01** dispara quando um mesmo IP falha o login em cinco contas diferentes
> em dez minutos — o que caracteriza um ataque distribuído, não um usuário que
> esqueceu a senha. E a **RD02** observa callbacks com assinatura inválida.
>
> Repare que as duas vigiam exatamente os pontos que protegemos nas etapas
> anteriores: elas existem para o caso de aqueles controles falharem ou serem
> contornados.

---

#### Bloco 6B — Regra RD03 e roteiro de resposta
**Responsável:** Bruna · **Duração:** 6:05 – 6:20 · **Tela:** `roteiros/etapa-6-deteccao-de-intrusoes.md`, Seções 6.4 e 6.5

**Direção.** Apresentar a terceira regra e resumir as quatro fases da resposta a
incidentes.

**Fala sugerida.**

> A **RD03** trata do caso de abuso CA04: dispara quando um mesmo usuário
> consulta mais de trinta perfis diferentes em menos de dois minutos, volume
> típico de ferramenta automatizada, não de navegação humana.
>
> E o roteiro de resposta define o que acontece depois do alerta, em quatro
> fases: triagem nos primeiros quinze minutos, contenção com bloqueio
> temporário, erradicação com troca forçada de credenciais e recuperação. Se
> houver vazamento efetivo de dados pessoais, entra a notificação prevista na
> LGPD.

---

#### Bloco 7A — Pipeline DevSecOps
**Responsável:** Gabriela · **Duração:** 6:20 – 6:40 · **Tela:** `roteiros/etapa-7-devsecops-e-video-final.md`, Seção 7.1

**Direção.** Percorrer os momentos do pipeline associando cada um à evidência
que produz.

**Fala sugerida.**

> Para fechar, a Etapa 7 conecta tudo isso a um pipeline DevSecOps.
>
> [mostrar a tabela do pipeline]
>
> A ideia é que a segurança não seja uma fase no fim do projeto, e sim uma
> atividade presente em cada momento. No planejamento, o STRIDE e a análise de
> riscos. No código, as práticas seguras e os testes. Na verificação, o ZAP. E na
> operação, os logs e as regras de detecção.
>
> Cada momento produz uma evidência — e é essa evidência que autoriza seguir
> para o próximo.

---

#### Bloco 7B — Gates de segurança
**Responsável:** Inaurrara · **Duração:** 6:40 – 6:52 · **Tela:** `roteiros/etapa-7-devsecops-e-video-final.md`, Seção 7.2

**Direção.** Apresentar os quatro gates e enfatizar a exigência de evidência e
responsável em cada bloqueio.

**Fala sugerida.**

> Definimos quatro gates de segurança, que são as condições que impedem a
> continuidade do pipeline.
>
> [mostrar a tabela de gates]
>
> Teste de segurança reprovado, vulnerabilidade crítica sem análise, segredo
> encontrado no repositório e artefato obrigatório sem evidência.
>
> O ponto central é que cada bloqueio exige evidência, responsável e decisão
> registrada. Uma exceção precisa de justificativa, prazo e controle
> compensatório — nunca simplesmente ignorar o alerta.

---

#### Bloco 7C — Aprendizados e encerramento
**Responsável:** Erik · **Duração:** 6:52 – 7:00 · **Tela:** `roteiros/etapa-7-devsecops-e-video-final.md`, Seção 7.3

**Direção.** Sintetizar o aprendizado do grupo, assumir a principal limitação do
trabalho e encerrar.

**Fala sugerida.**

> O principal aprendizado do grupo foi perceber que a segurança não vem de um
> controle isolado, e sim de uma cadeia rastreável: da ameaça ao risco, do risco
> ao requisito, do requisito à decisão de arquitetura, ao código, ao teste e ao
> gate de entrega.
>
> Nossa maior limitação foi não ter uma implementação executável, o que impede
> confirmar empiricamente a eficácia dos controles propostos.
>
> Obrigado pela atenção!

---

### 7.4.4 Cobertura dos itens obrigatórios

| Item exigido pelo enunciado | Bloco | Responsável |
| :--- | :---: | :--- |
| 1. Sistema escolhido | 1 | Bruna |
| 2. Principais ameaças e casos de abuso | 2A | Gabriela |
| 3. Riscos prioritários | 2B | Inaurrara |
| 4. Decisões de arquitetura | 3 | Erik |
| 5. Práticas de código seguro | 4A e 4B | Bruna e Erik |
| 6. Principais resultados da verificação | 5A e 5B | Bruna e Gabriela |
| 7. Regras de detecção | 6A e 6B | Inaurrara e Bruna |
| 8. Pipeline DevSecOps proposto | 7A e 7B | Gabriela e Inaurrara |
| 9. Aprendizados do grupo | 7C | Erik |

Todos os nove itens estão cobertos e os quatro integrantes participam da
apresentação, atendendo à exigência de participação individual. A distribuição
segue a contribuição de cada um nas etapas correspondentes, de modo que a
avaliação individual possa ser relacionada aos commits do repositório.

### 7.4.5 Margem de tempo e ajustes possíveis

O roteiro está dimensionado em 7 minutos, com 1 minuto de margem até o limite
de 8. Cada integrante deve cronometrar a própria fala antes da gravação
definitiva, pois a leitura tende a ser mais lenta do que a estimativa.

Caso a gravação ultrapasse o previsto, os cortes devem ocorrer nesta ordem,
preservando os itens obrigatórios:

1. Reduzir a rolagem de tabelas nos Blocos 2A e 4A, mantendo apenas a menção ao
   volume do mapeamento.
2. Resumir as alternativas descartadas no Bloco 3 a um único exemplo.
3. Encurtar a descrição do ambiente de laboratório no Bloco 5A, mantendo a
   ressalva metodológica, que não deve ser suprimida.

Nenhum dos nove itens obrigatórios pode ser removido para ajuste de tempo.
---

## 7.5 Entrega do Vídeo Final

Esta seção apresenta a entrega oficial da apresentação em vídeo do projeto Nexora, consolidando todo o trabalho de modelagem de ameaças, análise de riscos, arquitetura segura, práticas de código, verificação e detecção de intrusões desenvolvido ao longo da disciplina.

### 7.5.1 Ficha Técnica da Produção e Coordenação
*   **Coordenação e Edição de Vídeo:** Bruna Rosa Ferreira
*   **Software de Edição Utilizado:** CapCut Desktop
*   **Plataforma de Hospedagem:** YouTube (Modo Não-Listado)
*   **Duração Planejada/Final:** A PREENCHER
*   **Formato de Gravação:** Capturas de tela individuais com áudio dos integrantes, unificados em uma linha do tempo única.

### 7.5.2 Divisão de Participações no Vídeo
Para garantir que a avaliação individual seja justa e rastreável pelo professor Silvio, a apresentação foi dividida conforme a contribuição de cada integrante nas etapas do projeto (ESSA PARTE AINDA PODE SER MODIFICADA DEPOIS, GALERINHA):
1.  **Introdução e Apresentação do Sistema (Nexora):** Bruna
2.  **Modelagem de Ameaças (STRIDE) e Análise de Riscos (NIST):** Gabriela e Inaurrara
3.  **Arquitetura Segura e Decisões de Projeto:** Erik
4.  **Código Seguro e Casos de Teste (MFA / Validação de Callback):** Bruna e Erik
5.  **Verificação de Vulnerabilidades (OWASP ZAP / Juice Shop):** Bruna e Gabriela
6.  **Monitoramento, Regras de Detecção e Resposta:** Bruna e Inaurrara
7.  **Pipeline DevSecOps, Gates de Segurança e Aprendizados:** Gabriela, Inaurrara e Erik

---

### 7.5.3 Links e Evidência de Visualização

*   **🎬 LINK DO VÍDEO NO YOUTUBE (NÃO-LISTADO):** [Clique aqui para assistir à apresentação do Projeto Nexora (AINDA Ñ FOI GRAVADO)](#)
*   **📂 PASTA DE EVIDÊNCIAS DE SETUP:** `evidencias/etapa-5/`
