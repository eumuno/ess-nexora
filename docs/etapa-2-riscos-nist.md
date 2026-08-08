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
*(PARTE DE INAURRARA; favor, apagar depois de preencher)*

---

## 2.6 Priorização dos Riscos
*(PARTE DE INAURRARA; favor, apagar depois de preencher)*

---

## 2.7 Estratégias de Tratamento
*(PARTE DO ERIK; favor, apagar depois de preencher)*

---

## 2.8 Apresentação das Funções do NIST CSF 2.0
*(PARTE DO ERIK; favor, apagar depois de preencher)*

---

## 2.9 Mapeamento dos Riscos para as Funções do NIST CSF
*(PARTE DO ERIK; favor, apagar depois de preencher)*

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
