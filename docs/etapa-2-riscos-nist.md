# 2 — Análise, Priorização e Tratamento de Riscos com o NIST CSF 2.0

Esta etapa transforma as ameaças e os casos de abuso da Etapa 1 em riscos avaliáveis e comparáveis. A avaliação é inicial e considera as características conhecidas da Nexora e as condições hipotéticas identificadas durante a modelagem. Os valores deverão ser revistos caso a arquitetura, os controles existentes ou evidências de implementação alterem o contexto.

## 2.1 Critérios de Probabilidade
A probabilidade de ocorrência de um evento de risco foi classificada em uma escala de 1 a 4, de forma justificada pelas características técnicas do sistema, contexto de uso e plausibilidade de exploração de vulnerabilidades:

| Valor | Classificação | Critério de Avaliação | Exemplos Aplicados à Plataforma Nexora |
| :---: | :---: | :--- | :--- |
| **1** | **Baixa** | O evento depende de condições altamente incomuns, acesso físico/lógico extremamente específico ou altíssima capacidade técnica e esforço por parte do atacante. | Engenharia reversa complexa do código ofuscado do player de vídeo ou quebra de criptografia TLS em trânsito de chaves privadas. |
| **2** | **Média-baixa** | O evento é possível, mas depende da existência de uma vulnerabilidade técnica ou condição operacional muito específica e pouco comum. | Exploração de uma falha de estouro de buffer em uma biblioteca específica e desatualizada de processamento de certificados em PDF. |
| **3** | **Média-alta** | O evento é perfeitamente plausível e pode ocorrer em situações comuns de uso da plataforma ou por meio de técnicas amplamente conhecidas de ataque. | Realização de ataques de engenharia social (phishing) para roubo de credenciais ou preenchimento de senhas fracas reutilizadas. |
| **4** | **Alta** | O evento pode ocorrer com extrema facilidade, alta frequência ou durante condições normais e previsíveis de alta demanda do sistema. | Queda ou severa lentidão dos servidores de streaming e banco de dados devido ao excesso de acessos simultâneos no encerramento de prazos. |

---

## 2.2 Critérios de Impacto
O impacto de um incidente foi avaliado em uma escala de 1 a 4, considerando potenciais prejuízos financeiros, operacionais, reputacionais, violação de propriedade intelectual e descumprimento de obrigações legais (como a LGPD):

| Valor | Classificação | Critério de Avaliação | Exemplos Aplicados à Plataforma Nexora |
| :---: | :---: | :--- | :--- |
| **1** | **Baixo** | Causa pequenos transtornos operacionais e de usabilidade que não afetam as funções críticas da plataforma e são corrigidos rapidamente. | Indisponibilidade momentânea do contador de progresso de visualização de aulas ou falhas estéticas pontuais no fórum. |
| **2** | **Moderado** | Causa interrupções ou inconsistências temporárias em serviços não vitais, existindo alta probabilidade de recuperação rápida e contorno operacional. | Lentidão temporária no carregamento da emissão de certificados históricos, sem perda permanente de dados ou suspensão de aulas. |
| **3** | **Alto** | Causa prejuízo severo e relevante aos usuários (alunos/instrutores), ao negócio da plataforma, à administração ou à privacidade. | Vazamento de dados pessoais de alunos (CPF, e-mail) protegidos pela LGPD, ou perda de progresso acadêmico consolidado e notas de exames. |
| **4** | **Muito alto** | Pode afetar muitos usuários simultaneamente, comprometer funções críticas de negócio de forma prolongada ou acarretar severos prejuízos regulatórios e jurídicos. | Comprometimento total das chaves privadas do gateway de pagamento, sequestro de dados (Ransomware), ou interrupção do streaming em exames finais. |

---

## 2.3 Cálculo e Classificação dos Riscos
O nível de criticidade (pontuação) de cada risco é obtido pelo produto entre a Probabilidade (P) e o Impacto (I):

$$\text{Pontuação do Risco} = \text{Probabilidade} \times \text{Impacto}$$

A classificação final do nível de criticidade segue a matriz abaixo:

| Pontuação | Nível do Risco | Ação Recomendada |
| :---: | :---: | :--- |
| **1 a 3** | **Baixo** | Monitoramento periódico. O risco pode ser aceito se o custo do controle for desproporcional. |
| **4 a 7** | **Médio** | Planejamento de ações preventivas e melhorias operacionais no médio prazo. |
| **8 a 11** | **Alto** | Planejamento e mitigação prioritária com controles robustos de curto prazo. |
| **12 a 16** | **Crítico** | Mitigação imediata e mandatória. Requer controles impeditivos antes da disponibilização em produção. |

---

## 2.4 Registro de Riscos
Esta tabela mapeia as ameaças da Etapa 1 a eventos de risco operacional específicos da plataforma Nexora. As linhas correspondentes a categorias de ameaças ainda não modeladas na Etapa 1 foram deixadas como placeholders estruturais.

| ID | Origem STRIDE | Evento de Risco | Vulnerabilidade ou Condição | Probabilidade (P) | Impacto (I) | Pontuação (P×I) | Nível do Risco |
| :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: |
| **R01** | Spoofing (T01) | Um atacante acessa a conta de um estudante ou instrutor por meio de técnicas de força bruta ou preenchimento de credenciais (*credential stuffing*). | Uso de credenciais fracas ou reutilizadas combinada à ausência de autenticação multifator (MFA) e de bloqueios de tentativas malsucedidas (*rate limiting*). | 3 | 3 | **9** | **Alto** |
| **R02** | Spoofing (T02) | Um agente malicioso cadastra-se como um instrutor falso, fraudando alunos e prejudicando a marca Nexora. | Ausência de uma política de validação manual de documentos e de homologação de identidade para novos perfis de instrutor. | 2 | 3 | **6** | **Médio** |
| **R03** | Spoofing (T03) | Ataques de phishing em massa induzem alunos a fornecer suas credenciais em domínios falsos que simulam o remetente da Nexora. | Falta ou configuração incorreta das diretivas e registros de autenticação de e-mail (SPF, DKIM, DMARC) no domínio institucional da plataforma Nexora. | 3 | 3 | **9** | **Alto** |
| **R04** | Spoofing (T04) | Um atacante forja notificações HTTP POST de callback simulando confirmação de transação aprovada e liberando cursos sem pagar. | Falha de validação da origem e integridade das requisições de retorno (falta de assinatura HMAC ou de verificação reversa de status na API do gateway). | 2 | 3 | **6** | **Médio** |
| *R05* | *Tampering* | *(AGUARDANDO ERIK C/ O STRIDE: irei preencher com os riscos derivados de Tampering da Etapa 1)* | *A preencher* | - | - | - | - |
| *R06* | *Repudiation* | *(AGUARDANDO ERIK C/ O STRIDE: irei preencher com os riscos derivados de Repudiation da Etapa 1)* | *A preencher* | - | - | - | - |
| *R07* | *Information Disclosure* | *(AGUARDANDO ERIK C/ O STRIDE: irei preencher com os riscos de vazamento de dados pessoais/financeiros)* | *A preencher* | - | - | - | - |
| *R08* | *Denial of Service* | *(AGUARDANDO ERIK C/ O STRIDE: irei preencher com riscos de indisponibilidade de streaming/servidor)* | *A preencher* | - | - | - | - |
| *R09* | *Elevation of Privilege* | *(AGUARDANDO ERIK C/ O STRIDE: irei preencher com riscos de escalação de privilégios para admin)* | *A preencher* | - | - | - | - |

---

## 2.5 Justificativa das Avaliações

As avaliações a seguir detalham os quatro riscos que já possuem evento,
condição, probabilidade e impacto definidos no registro da Seção 2.4. Os riscos
R05 a R09 ainda não podem ser justificados porque seus eventos e valores
dependem da conclusão das demais categorias STRIDE na Etapa 1.

### R01 — Uso indevido de conta por credential stuffing

**Origem e caso de abuso relacionado:** T01 — *Spoofing*; CA03 — Sequestro de
conta de instrutor para desvio de repasses financeiros.

**Justificativa da probabilidade:** a probabilidade foi classificada como 3 —
média-alta — porque a reutilização de senhas e a exposição de credenciais em
outros serviços tornam o *credential stuffing* plausível contra uma plataforma
acessível pela internet. O ataque pode ser automatizado contra o serviço de
autenticação, embora ainda dependa de credenciais válidas reutilizadas e da
ausência de MFA ou limitação eficaz de tentativas.

**Justificativa do impacto:** o impacto foi classificado como 3 — alto — porque
uma conta comprometida permite consultar dados pessoais, utilizar cursos e
executar ações em nome da vítima. No caso de um instrutor, o atacante também
pode alterar dados de repasse, acessar informações de alunos e causar prejuízo
financeiro direto. O alcance inicial permanece vinculado às contas efetivamente
comprometidas, razão pela qual não foi adotado o valor 4.

**Usuários, ativos e componentes afetados:** alunos, instrutores e, se forem
alvo, usuários privilegiados; credenciais, sessões, dados pessoais, cursos,
informações financeiras, serviço de autenticação e API.

**Possíveis consequências e adequação do nível:** invasão de conta, exposição
de dados, desvio de repasses, ações fraudulentas e perda de confiança.
Probabilidade 3 × impacto 3 = 9 — risco alto. A classificação é adequada porque
o evento é plausível e produz prejuízo relevante, mas não compromete
necessariamente toda a plataforma em uma única ocorrência.

### R02 — Cadastro de instrutor falso

**Origem e caso de abuso relacionado:** T02 — *Spoofing*. A Etapa 1 ainda não
possui um caso de abuso específico para o cadastro de instrutor falso.

**Justificativa da probabilidade:** a probabilidade foi classificada como 2 —
média-baixa — porque o abuso depende de uma condição específica: inexistência
ou insuficiência do processo de validação documental e homologação de novos
instrutores. O cadastro é acessível e não exige grande capacidade técnica, mas
o agente ainda precisa criar um perfil convincente e obter aprovação ou
publicação na plataforma.

**Justificativa do impacto:** o impacto foi classificado como 3 — alto — porque
alunos podem adquirir cursos fraudulentos, um profissional legítimo pode ter
sua identidade utilizada indevidamente e a Nexora pode enfrentar estornos,
denúncias e dano reputacional. O alcance tende a ficar inicialmente limitado
aos usuários que interagem com o perfil falso.

**Usuários, ativos e componentes afetados:** alunos, instrutores legítimos,
moderação, suporte e equipe financeira; perfis e permissões, catálogo, cursos,
pagamentos, painel administrativo e reputação da plataforma.

**Possíveis consequências e adequação do nível:** fraude, prejuízo financeiro,
estornos, remoção de conteúdo e perda de confiança. Probabilidade 2 × impacto 3
= 6 — risco médio. O nível reflete um impacto relevante condicionado a uma
falha específica no processo de homologação.

### R03 — Phishing com e-mails falsos

**Origem e caso de abuso relacionado:** T03 — *Spoofing*. A Etapa 1 ainda não
possui um caso de abuso específico para campanhas de phishing.

**Justificativa da probabilidade:** a probabilidade foi classificada como 3 —
média-alta — porque mensagens falsas podem ser distribuídas em escala e com
baixo custo, aproveitando comunicações previsíveis da Nexora, como recuperação
de conta e avisos de cursos. O sucesso ainda depende de a vítima confiar na
mensagem e fornecer suas credenciais, o que impede a classificação como 4.

**Justificativa do impacto:** o impacto foi classificado como 3 — alto — porque
o sucesso da campanha pode comprometer contas, expor dados e permitir ações em
nome das vítimas. O dano varia conforme o perfil atingido e depende da
interação individual de cada destinatário.

**Usuários, ativos e componentes afetados:** principalmente alunos e
instrutores, podendo alcançar usuários internos; credenciais, sessões, serviço
de notificações, domínio de e-mail e serviço de autenticação.

**Possíveis consequências e adequação do nível:** roubo de credenciais,
invasão de contas, fraude e dano reputacional. Probabilidade 3 × impacto 3 = 9
— risco alto, em razão da plausibilidade, da escala de distribuição e do
prejuízo relevante às vítimas que interagirem com a campanha.

### R04 — Forjamento de callback de pagamento

**Origem e caso de abuso relacionado:** T04 — *Spoofing*. A Etapa 1 ainda não
possui um caso de abuso específico para o forjamento de callbacks do gateway.

**Justificativa da probabilidade:** a probabilidade foi classificada como 2 —
média-baixa — porque o ataque depende do conhecimento do endpoint de retorno e
de uma falha específica na validação da origem, assinatura ou correspondência
da notificação com o pedido registrado.

**Justificativa do impacto:** o impacto foi classificado como 3 — alto — porque
callbacks falsos podem liberar cursos sem pagamento, gerar inconsistências na
conciliação e afetar os repasses aos instrutores. O impacto foi mantido em 3
porque a extensão depende da quantidade de pedidos forjados e da capacidade de
detecção e reversão das liberações.

**Usuários, ativos e componentes afetados:** Nexora, instrutores, compradores
legítimos e equipe financeira; API, gateway de pagamento, pedidos, transações,
assinaturas e conteúdo educacional.

**Possíveis consequências e adequação do nível:** perda de receita, acesso
indevido a conteúdo, divergências financeiras e contestações. Probabilidade 2 ×
impacto 3 = 6 — risco médio. O nível é coerente com uma exploração dependente
de vulnerabilidade específica, mas capaz de causar prejuízo relevante.

### Dependências para R05 a R09

As justificativas de R05, R06, R07, R08 e R09 deverão ser acrescentadas depois
que a Etapa 1 definir as ameaças correspondentes e a Seção 2.4 registrar seus
eventos, condições e notas. Nenhum valor foi presumido para evitar quebrar a
rastreabilidade entre ameaça e risco.

---

## 2.6 Priorização dos Riscos

A priorização abaixo é **provisória** e considera somente R01 a R04, os riscos
que já possuem avaliação completa. A lista final deverá incluir R05 a R09 assim
que seus valores forem definidos.

| Prioridade provisória | Risco | Pontuação | Nível | Justificativa da prioridade |
| :---: | :--- | :---: | :---: | :--- |
| **1** | **R01 — Uso indevido de conta** | **9** | **Alto** | Além de possuir a maior pontuação disponível, já está materializado no CA03 e pode atingir dados pessoais, cursos e repasses financeiros de instrutores. |
| **2** | **R03 — Phishing com e-mails falsos** | **9** | **Alto** | Pode ser executado em escala e funcionar como porta de entrada para o comprometimento de várias contas, mas depende da interação das vítimas. |
| **3** | **R04 — Forjamento de callback de pagamento** | **6** | **Médio** | Afeta diretamente a integridade financeira e a liberação de conteúdo, porém depende de uma falha específica na integração com o gateway. |
| **4** | **R02 — Cadastro de instrutor falso** | **6** | **Médio** | Pode causar fraude e dano reputacional, mas tende a ter alcance inicial mais limitado e admite contenção pela revisão e remoção do perfil. |

### Justificativa da ordem provisória

R01 e R03 possuem a mesma pontuação. R01 recebeu precedência porque o CA03 já
demonstra uma consequência financeira concreta — o desvio de repasses — e uma
conta de instrutor comprometida também expõe conteúdo e informações de alunos.
R03 permanece em segundo lugar devido à possibilidade de distribuição em
escala e de originar múltiplos comprometimentos de conta.

R04 e R02 também estão empatados. R04 foi colocado antes por afetar a
integridade do processamento financeiro e permitir liberação automática de
conteúdo sem receita. R02 tende a iniciar com um único perfil fraudulento e
pode ser contido por procedimentos de moderação, apesar de ainda exigir
tratamento.

Esta ordem não deve ser utilizada como priorização final da Etapa 2 nem como
base exclusiva para selecionar novos requisitos enquanto R05 a R09 permanecerem
sem avaliação. A lista deverá ser recalculada considerando pontuação, alcance,
criticidade dos ativos, possibilidade de recuperação, dependências e urgência.

---

## 2.7 Estratégias de Tratamento
Definida a criticidade de cada risco, esta seção estabelece **como** cada um
será tratado. O tratamento não se confunde com o controle técnico: primeiro o
grupo decide a postura diante do risco, e só depois seleciona as medidas
concretas, detalhadas na Seção 2.10.

Foram consideradas as quatro estratégias clássicas de tratamento:

| Estratégia | Descrição | Quando é apropriada na Nexora |
| :--- | :--- | :--- |
| **Evitar** | Eliminar a atividade, funcionalidade ou condição que dá origem ao risco. | Somente quando a funcionalidade não for essencial ao negócio, o que raramente ocorre em uma plataforma cuja operação depende de cadastro aberto, pagamentos e streaming. |
| **Reduzir** | Implementar controles que diminuam a probabilidade de ocorrência, o impacto, ou ambos. | Estratégia predominante, aplicável quando a funcionalidade precisa continuar existindo, mas pode receber salvaguardas adicionais. |
| **Compartilhar** | Atribuir parte da operação, da execução do controle ou de suas consequências a um terceiro. | Aplicável às integrações externas da Nexora — gateway de pagamento, provedor de e-mail, serviço de streaming e infraestrutura em nuvem. |
| **Aceitar** | Reconhecer e manter conscientemente o risco, com justificativa formal e acompanhamento periódico. | Aplicável ao risco residual que permanece após o tratamento, quando o custo de reduzi-lo ainda mais for desproporcional ao benefício. |

Aceitar um risco não significa ignorá-lo. A decisão precisa ser justificada,
aprovada por uma pessoa responsável e revista sempre que a arquitetura, os
controles ou o contexto de operação da Nexora mudarem.

Compartilhar também não transfere a responsabilidade final. Ainda que a Nexora
delegue o processamento de pagamentos a um gateway externo, permanece
responsável por validar as respostas recebidas e por responder aos seus
usuários e às autoridades competentes.

### 2.7.1 Estratégia escolhida para cada risco

| Risco | Nível inicial | Estratégia principal | Justificativa da escolha |
| :--- | :---: | :--- | :--- |
| **R01 — Uso indevido de conta** | Alto (9) | **Reduzir** | O acesso remoto autenticado é a função central da plataforma e não pode ser eliminado. A probabilidade é reduzida com MFA para perfis privilegiados e limitação de tentativas, conforme o requisito RS01. |
| **R02 — Cadastro de instrutor falso** | Médio (6) | **Reduzir** | O cadastro aberto de instrutores é o próprio modelo de negócio de um marketplace educacional; evitá-lo eliminaria a oferta de cursos. A homologação documental reduz a probabilidade sem impedir a entrada de novos profissionais. |
| **R03 — Phishing com e-mails falsos** | Alto (9) | **Reduzir e compartilhar** | SPF, DKIM e DMARC reduzem a aceitação de mensagens falsificadas, mas a eficácia depende dos servidores de destino e do provedor de envio, que executam parte do controle fora da infraestrutura da Nexora. |
| **R04 — Forjamento de callback de pagamento** | Médio (6) | **Reduzir e compartilhar** | A validação HMAC e a reconsulta ao gateway reduzem o risco, enquanto a emissão da assinatura, a rotação das chaves e a confirmação da transação pertencem ao provedor de pagamento contratado. |
| **R05 — Adulteração de registros acadêmicos e financeiros** *(Tampering)* | A definir na Seção 2.4 | **Reduzir** | As operações de progresso, avaliação, compra e publicação de conteúdo precisam continuar disponíveis. O tratamento atua sobre a validação no servidor e sobre a integridade dos registros, não sobre a existência da funcionalidade. |
| **R06 — Negação de operações realizadas** *(Repudiation)* | A definir na Seção 2.4 | **Reduzir e aceitar o residual** | Registros completos e protegidos reduzem substancialmente as contestações sem prova, mas nenhum controle elimina a possibilidade de um usuário negar uma operação. O residual é aceito de forma consciente, condicionado à existência de trilha de auditoria confiável. |
| **R07 — Exposição de dados pessoais e conteúdo pago** *(Information Disclosure)* | A definir na Seção 2.4 | **Reduzir** | Os dados são necessários à operação e à emissão de certificados, de modo que evitar sua coleta não é viável. O tratamento concentra-se em autorização no servidor, minimização e monitoramento de consultas anômalas. |
| **R08 — Indisponibilidade da plataforma** *(Denial of Service)* | A definir na Seção 2.4 | **Reduzir e compartilhar** | Limitação de requisições e monitoramento de capacidade são responsabilidade da Nexora, enquanto a absorção de tráfego volumétrico e a entrega de vídeo dependem de provedores de nuvem e de distribuição de conteúdo contratados. |
| **R09 — Elevação indevida de privilégio** *(Elevation of Privilege)* | A definir na Seção 2.4 | **Reduzir** | As funções administrativas, de moderação e financeiras são indispensáveis à operação. O tratamento incide sobre a verificação de autorização no servidor e sobre a revisão periódica das permissões concedidas. |

### 2.7.2 Observações sobre as estratégias não adotadas

**Nenhum risco recebeu a estratégia Evitar.** A eliminação da atividade de
origem exigiria suprimir funcionalidades que definem a Nexora: autenticação
remota, cadastro aberto de instrutores, pagamentos on-line, emissão de
certificados e distribuição de videoaulas. Como o custo de negócio seria
integral e a redução por controles é viável em todos os casos, a estratégia foi
descartada de forma justificada, e não por omissão.

**A estratégia Aceitar não foi adotada de forma isolada em nenhum risco.** Ela
aparece apenas em R06, e somente sobre o residual que permanece depois da
redução. Aceitar integralmente um risco alto ou crítico antes de qualquer
tratamento seria incompatível com a criticidade dos ativos identificados na
Seção 1.3.2, especialmente credenciais, dados pessoais e registros financeiros.

**As estratégias de R05 a R09 permanecem válidas independentemente dos valores
finais de probabilidade e impacto**, pois derivam da natureza da categoria
STRIDE de origem e da essencialidade da funcionalidade envolvida. Os níveis
iniciais deverão ser preenchidos assim que a Seção 2.4 for concluída, e os
rótulos dos riscos deverão ser alinhados aos nomes definitivos registrados
naquela seção.

---

## 2.8 Apresentação das Funções do NIST CSF 2.0

O **NIST Cybersecurity Framework 2.0** foi adotado para organizar os resultados
de segurança esperados no tratamento dos riscos da Nexora. O framework não
prescreve tecnologias nem produtos: ele descreve **resultados** que uma
organização deve alcançar, permitindo que cada projeto escolha os controles
adequados ao seu contexto.

A versão 2.0 organiza esses resultados em seis funções. A função **Govern** é a
principal novidade em relação à versão anterior e reconhece que decisões de
segurança dependem de responsabilidades, políticas e critérios definidos, e não
apenas de mecanismos técnicos.

| Função | Finalidade geral | Aplicação no projeto Nexora |
| :--- | :--- | :--- |
| **Govern** | Estabelecer políticas, papéis, responsabilidades e critérios de aceitação de riscos. | Definir quem aprova a homologação de instrutores, quem responde pelas chaves do gateway, qual o critério de risco residual aceitável e com que periodicidade as permissões são revisadas. |
| **Identify** | Conhecer os ativos, dependências, vulnerabilidades e riscos do sistema. | Corresponde ao trabalho já realizado nas Seções 1.3 e 1.4 e neste registro de riscos: inventário de ativos, perfis de acesso, fluxos de dados, limites de confiança e dependências externas. |
| **Protect** | Implementar salvaguardas que reduzam a probabilidade ou o impacto dos incidentes. | Autenticação multifator, limitação de tentativas, autorização verificada no servidor, validação de assinatura HMAC, minimização de dados e proteção do conteúdo pago. |
| **Detect** | Identificar eventos suspeitos, falhas e possíveis incidentes. | Registro de eventos de autenticação, callbacks rejeitados, consultas anômalas a perfis, alertas de capacidade e monitoramento das operações administrativas. |
| **Respond** | Conter, analisar, comunicar e tratar os incidentes detectados. | Bloqueio temporário de origem, suspensão preventiva de contas privilegiadas, congelamento da liberação de cursos e comunicação às equipes de segurança, financeira e jurídica. |
| **Recover** | Restaurar serviços e dados e reduzir os prejuízos causados. | Redefinição de credenciais, reversão de alterações indevidas em matrículas e certificados, reprocessamento de conciliações financeiras e restabelecimento do acesso após confirmação de identidade. |

As funções não são etapas sequenciais nem possuem a mesma relevância para todos
os riscos. Um risco pode exigir forte atuação em Protect e pouca em Recover, ou
o inverso. Elas também não substituem a análise de risco: indicam quais
resultados precisam ser considerados, e não quais controles devem ser
implementados.

---

## 2.9 Mapeamento dos Riscos para as Funções do NIST CSF
A tabela cruzada indica quais funções do NIST CSF 2.0 precisam ser consideradas
no tratamento de cada risco identificado. A marcação significa que a função
produz um resultado relevante para aquele risco; ela não implica que todas as
funções tenham o mesmo peso ou a mesma quantidade de controles associados.

| Risco | Govern | Identify | Protect | Detect | Respond | Recover |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **R01 — Uso indevido de conta** | X | X | X | X | X | X |
| **R02 — Cadastro de instrutor falso** | X | X | X | X | X | X |
| **R03 — Phishing com e-mails falsos** | X | X | X | X | X | — |
| **R04 — Forjamento de callback de pagamento** | X | X | X | X | X | X |
| **R05 — Adulteração de registros** *(Tampering)* | X | X | X | X | X | X |
| **R06 — Negação de operações** *(Repudiation)* | X | — | X | X | X | — |
| **R07 — Exposição de dados** *(Information Disclosure)* | X | X | X | X | X | X |
| **R08 — Indisponibilidade** *(Denial of Service)* | X | X | X | X | X | X |
| **R09 — Elevação de privilégio** *(Elevation of Privilege)* | X | X | X | X | X | X |

### 2.9.1 Justificativa das lacunas do mapeamento

As três ausências de marcação são deliberadas e foram justificadas para evitar
o preenchimento automático de todas as células, que esvaziaria o valor
analítico da tabela.

**R03 — Recover não marcado.** O phishing ocorre fora da infraestrutura da
Nexora: a mensagem falsa é enviada por um terceiro e nada da plataforma é
danificado pelo envio em si. Não há serviço a restaurar nem dado a recuperar. A
consequência recuperável do phishing é o comprometimento da conta, que já está
coberto pela função Recover do risco R01, para o qual esse cenário funciona
como vetor de entrada.

**R06 — Identify não marcado.** A negação de uma operação não decorre de um
ativo desconhecido ou de uma dependência não mapeada, e sim da insuficiência
dos registros produzidos. O tratamento se concentra em definir o que deve ser
registrado (Govern), garantir a produção e a integridade dos registros
(Protect), consultá-los diante de uma contestação (Detect) e conduzir a
apuração (Respond).

**R06 — Recover não marcado.** Uma trilha de auditoria não restaura serviço nem
reverte estado. Se uma contestação revelar uma alteração indevida que precise
ser desfeita, a reversão pertence ao risco de adulteração (R05), e não ao risco
de repúdio. Manter a distinção preserva a rastreabilidade entre cada risco e o
resultado de segurança que ele efetivamente exige.

### 2.9.2 Leitura do mapeamento

Todos os riscos exigem atuação em **Govern**, porque cada um depende de uma
decisão registrada: quem aprova instrutores, quem custodia as chaves do
gateway, qual o limite aceitável de indisponibilidade e com que periodicidade as
permissões privilegiadas são revisadas.

A concentração em **Protect** e **Detect** confirma o que a Etapa 1 já indicava:
a maior parte das ameaças da Nexora decorre de validação insuficiente no
servidor sobre entradas originadas fora do limite de confiança, situação que se
trata prevenindo a operação indevida e registrando a tentativa.

Os riscos com marcação nas seis funções — R01, R02, R04, R05, R07, R08 e R09 —
são aqueles em que um incidente produz um estado que precisa ser desfeito:
conta comprometida, curso liberado sem pagamento, matrícula alterada, dado
pessoal exposto ou serviço interrompido. São também os riscos que exigem o
plano de tratamento mais completo, detalhado na Seção 2.10.

---

## 2.10 Plano de Tratamento
Após a análise e classificação dos riscos operacionais identificados na plataforma Nexora, foi estruturado o plano de tratamento contendo as contramedidas específicas para cada cenário mapeado.

| Risco | Estratégia | Controles Propostos | Funções do NIST CSF | Responsáveis | Evidências e Verificação |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **R01 — Uso indevido de conta (Credential Stuffing)** | Reduzir | 1. Implementação de Autenticação Multifator (MFA) obrigatória para contas administrativas e de Instrutores.<br>2. Mecanismo de limite de tentativas de login (Rate Limiting) no servidor de autenticação.<br>3. Política de senhas fortes validada diretamente no servidor. | Protect, Detect, Respond | Equipe de Desenvolvimento e Infraestrutura | 1. Registro de testes comprovando o bloqueio de requisições após tentativas consecutivas inválidas.<br>2. Logs de auditoria registrando acessos suspeitos.<br>3. Fluxo de verificação de token MFA ativo em ambiente de testes. |
| **R02 — Cadastro de instrutor falso** | Reduzir | 1. Processo de validação de identidade (homologação manual) pela equipe de moderação antes de permitir a publicação de cursos.<br>2. Envio obrigatório de comprovação de documentos e portfólio no painel administrativo. | Govern, Protect | Equipe de Moderação e Gestão de Negócios | 1. Registro no banco de dados mostrando a alteração do status da conta após validação humana.<br>2. Pasta de armazenamento seguro contendo as cópias das documentações apresentadas. |
| **R03 — Ataques de phishing com e-mails falsos** | Reduzir | 1. Configuração e publicação das diretivas SPF (Sender Policy Framework), DKIM (DomainKeys Identified Mail) e DMARC (Domain-based Message Authentication) com política de rejeição no DNS do domínio. | Protect, Detect | Equipe de Infraestrutura e Redes | 1. Consulta pública ao DNS provando a existência e validade dos registros SPF, DKIM e DMARC do domínio da Nexora.<br>2. Relatórios de falha de entrega de e-mails de servidores não autorizados. |
| **R04 — Forjamento de notificações de pagamento** | Reduzir | 1. Validação de assinaturas criptográficas (HMAC-SHA256) em todas as requisições enviadas pelo gateway de pagamento.<br>2. Implementação de consulta ativa reversa à API oficial do gateway para revalidação do status da compra antes de liberar o curso. | Protect, Detect, Respond | Equipe de Desenvolvimento | 1. Relatório de testes automatizados simulando retornos sem assinatura e comprovando a rejeição pelo servidor.<br>2. Logs do sistema exibindo tentativas de callback sem token ou assinatura válidos sendo bloqueadas. |
| *R05 — Tampering* | *A preencher* | *(Aguardando Erik preencher com os riscos de Tampering da Etapa 1)* | *A preencher* | *A preencher* | *(Aguardando Erik)* |
| *R06 — Repudiation* | *A preencher* | *(Aguardando Erik preencher com os riscos de Repudiation da Etapa 1)* | *A preencher* | *A preencher* | *(Aguardando Erik)* |
| *R07 — Information Disclosure* | *A preencher* | *(Aguardando Erik preencher com os riscos de Information Disclosure da Etapa 1)* | *A preencher* | *A preencher* | *(Aguardando Erik)* |
| *R08 — Denial of Service* | *A preencher* | *(Aguardando Erik preencher com os riscos de Denial of Service da Etapa 1)* | *A preencher* | *A preencher* | *(Aguardando Erik)* |
| *R09 — Elevation of Privilege* | *A preencher* | *(Aguardando Erik preencher com os riscos de Elevation of Privilege da Etapa 1)* | *A preencher* | *A preencher* | *(Aguardando Erik)* |

---

## 2.11 Ordem Inicial de Implementação
A ordem inicial para a implantação das contramedidas de segurança foi planejada de acordo com as dependências do sistema, o impacto imediato das proteções e o esforço de engenharia necessário:

1. **Fase 1: Configuração DNS e e-mails seguros (R03):** Será a primeira ação pela baixíssima complexidade e rápido ganho reputacional. Trata-se de uma alteração no DNS institucional, sem impacto no código-fonte do portal.
2. **Fase 2: Políticas de login e bloqueios contra força bruta (R01):** Visa proteger o principal ponto de entrada de usuários antes do lançamento. A inserção do rate limiting e validações no servidor impede ataques em massa que possam indisponibilizar a plataforma.
3. **Fase 3: Integração e segurança financeira (R04):** Essencial para proteger as transações antes da plataforma transacionar valores reais. A validação de assinaturas HMAC garante a integridade de todas as matrículas vendidas.
4. **Fase 4: Moderação manual e onboarding de instrutores (R02):** Por envolver processos internos e rotinas de negócios (validação manual de documentos), este controle pode ser formalizado nas fases finais de testes operacionais do sistema.
5. **Fase 5: Integração de controles complementares:** Atualização do cronograma de desenvolvimento assim que as seções de riscos residuais de Tampering, Repudiation, Vazamento, DoS e Elevação de Privilégio (R05 a R09) forem especificadas pelo grupo.
---

## 2.12 Estimativa do Risco Residual
A tabela a seguir apresenta a projeção de mitigação esperada após a implementação funcional de todos os controles recomendados para os riscos atuais.

| Risco | Nível Inicial | Nível Residual Esperado | Condição para Aceitar o Residual |
| :--- | :--- | :--- | :--- |
| **R01** | Alto (9) | **Baixo (3)** <br>*(P: 1, I: 3)* | MFA ativado obrigatoriamente para as contas administrativas e bloqueio por IP (Rate Limiting) ativo em produção. |
| **R02** | Médio (6) | **Baixo (2)** <br>*(P: 1, I: 2)* | Implementação de fluxos operacionais internos de aprovação manual e relatórios de auditoria periódicos das contas de instrutores. |
| **R03** | Alto (9) | **Baixo (3)** <br>*(P: 1, I: 3)* | Logs de auditoria do DMARC apontando rejeição de e-mails não autorizados em conformidade com as políticas propagadas no DNS. |
| **R04** | Médio (6) | **Baixo (3)** <br>*(P: 1, I: 3)* | Verificação criptográfica HMAC implementada de forma síncrona no back-end e rotatividade mensal das chaves do gateway. |
| *R05* | *A preencher* | *A preencher* | *(Aguardando Erik preencher com os riscos de Tampering da Etapa 1)* |
| *R06* | *A preencher* | *A preencher* | *(Aguardando Erik preencher com os riscos de Repudiation da Etapa 1)* |
| *R07* | *A preencher* | *A preencher* | *(Aguardando Erik preencher com os riscos de Information Disclosure da Etapa 1)* |
| *R08* | *A preencher* | *A preencher* | *(Aguardando Erik preencher com os riscos de Denial of Service da Etapa 1)* |
| *R09* | *A preencher* | *A preencher* | *(Aguardando Erik preencher com os riscos de Elevation of Privilege da Etapa 1)* |

---

## 2.13 Considerações Finais da Etapa 2
A realização desta etapa demonstrou como traduzir os cenários teóricos identificados na modelagem de ameaças para planos práticos de mitigação baseados em custos, responsabilidades e viabilidade operacional. O uso das funções do NIST CSF 2.0 serviu como norteador de resultados, assegurando que o gerenciamento de incidentes na Nexora não se limite apenas à prevenção técnica, mas também contemple a governança e processos de negócio.

A lição mais relevante para o projeto reside no fato de que os controles de segurança precisam coexistir com a usabilidade e a viabilidade financeira da startup. Reduzir e tratar o risco residual, em vez de focar em uma busca irreal por risco zero, permite que o time dedique recursos técnicos aos pontos que efetivamente trariam consequências graves para o faturamento e para os dados dos usuários protegidos por lei.

Os controles estabelecidos nesta fase servirão de base para a Etapa 3, na qual os requisitos de segurança derivados guiarão a consolidação e o desenho da arquitetura segura e dos limites de confiança do software Nexora.
