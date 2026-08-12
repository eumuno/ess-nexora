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
Esta tabela realiza o mapeamento biunívoco de todas as 24 ameaças identificadas com o método STRIDE na Etapa 1 para eventos de risco específicos na plataforma Nexora.

| ID | Origem STRIDE | Evento de Risco | Vulnerabilidade ou Condição | Probabilidade (P) | Impacto (I) | Pontuação (P×I) | Nível do Risco |
| :---: | :--- | :--- | :--- | :---: | :---: | :---: | :---: |
| **R01** | Spoofing (T01) | Um atacante acessa a conta de um estudante ou instrutor por meio de técnicas de força bruta ou preenchimento de credenciais (*credential stuffing*). | Uso de credenciais fracas ou reutilizadas combinada à ausência de autenticação multifator (MFA) e de bloqueios de tentativas malsucedidas (*rate limiting*). | 3 | 3 | **9** | **Alto** |
| **R02** | Spoofing (T02) | Um agente malicioso cadastra-se como um instrutor falso, fraudando alunos e prejudicando a marca Nexora. | Ausência de uma política de validação manual de documentos e de homologação de identidade para novos perfis de instrutor. | 2 | 3 | **6** | **Médio** |
| **R03** | Spoofing (T03) | Ataques de phishing em massa induzem alunos a fornecer suas credenciais em domínios falsos que simulam o remetente da Nexora. | Falta ou configuração incorreta das diretivas e registros de autenticação de e-mail (SPF, DKIM, DMARC) no domínio institucional da plataforma Nexora. | 3 | 3 | **9** | **Alto** |
| **R04** | Spoofing (T04) | Um atacante forja notificações HTTP POST de callback simulando confirmação de transação aprovada e liberando cursos sem pagar. | Falha de validação da origem e integridade das requisições de retorno (falta de assinatura HMAC ou de verificação reversa de status na API do gateway). | 2 | 3 | **6** | **Médio** |
| **R05** | Tampering (T05) | Aluno intercepta e altera requisições de progresso para emitir certificados fraudulentos sem assistir ao conteúdo. | Ausência de validação rigorosa no servidor sobre o tempo de visualização e o progresso real do aluno antes de marcar uma aula como concluída. | 3 | 3 | **9** | **Alto** |
| **R06** | Tampering (T06) | Alteração do preço do curso ou manipulação de cupons no lado do cliente (front-end) antes do envio da transação. | O servidor de back-end aceita o preço enviado pelo cliente de forma cega, sem revalidar os valores contra a base de dados central no momento do checkout. | 3 | 3 | **9** | **Alto** |
| **R07** | Tampering (T07) | Alteração de respostas de avaliações acadêmicas ou da nota final através de chamadas diretas de API. | API de correção de avaliações não valida o papel de quem envia a requisição e carece de mecanismos de integridade nos dados enviados. | 2 | 3 | **6** | **Médio** |
| **R08** | Tampering (T08) | Emissão ou edição manual de certificados PDF falsificados contendo nomes de cursos ou alunos alterados. | PDF gerado estaticamente, sem aplicação de assinatura digital criptográfica institucional ou de código de validação pública no servidor. | 3 | 3 | **9** | **Alto** |
| **R09** | Tampering (T09) | Envio de arquivos executáveis maliciosos (malware) camuflados de material complementar (PDF/imagens) nos cursos. | Falta de verificação de tipo MIME real dos arquivos carregados e ausência de verificação de antivírus no servidor de upload. | 2 | 3 | **6** | **Médio** |
| **R10** | Tampering (T10) | Criação de perfis falsos de alunos para manipular a nota de reputação de cursos no catálogo. | Ausência de regras que limitem a avaliação de um curso apenas para alunos que efetivamente assistiram às aulas ou restrição de múltiplos cadastros sob a mesma origem. | 3 | 2 | **6** | **Médio** |
| **R11** | Tampering (T11) | Manipulação direta de pontuações, conquistas e posições de rankings no banco de dados ou via API de gamificação. | O servidor aceita requisições do front-end que adicionam pontos e desbloqueiam conquistas sem validar as regras lógicas de negócio de forma síncrona no back-end. | 2 | 1 | **2** | **Baixo** |
| **R12** | Repudiation (T12) | Usuário realiza compra de curso, consome o conteúdo e posteriormente alega desconhecer a transação para solicitar estorno. | O sistema não registra metadados de auditoria robustos (IP, dados do dispositivo, logs de consentimento e aceite de termos) no ato da compra. | 3 | 3 | **9** | **Alto** |
| **R13** | Repudiation (T13) | Instrutor altera, substitui ou exclui módulos de cursos já vendidos aos alunos e nega a ação. | Inexistência de versionamento histórico de conteúdo de cursos e falta de logs de auditoria invioláveis sobre alterações feitas por instrutores. | 2 | 2 | **4** | **Médio** |
| **R14** | Repudiation (T14) | Usuário com acesso privilegiado realiza ações críticas nocivas (exclusão de usuários/estornos) e apaga seus rastros de atividade. | Armazenamento de logs administrativos no mesmo servidor da aplicação, permitindo acesso de gravação e exclusão por usuários do painel. | 2 | 4 | **8** | **Alto** |
| **R15** | Info Disclosure (T15) | Um usuário autenticado comum acessa dados cadastrais privados (CPF, e-mail) de outros usuários da plataforma. | Falha de controle de acesso ao nível do objeto (BOLA/IDOR) no endpoint da API que exibe as informações cadastrais de perfis. | 3 | 3 | **9** | **Alto** |
| **R16** | Info Disclosure (T16) | Distribuição pirata e download direto de videoaulas em repositórios e fóruns externos da plataforma. | Armazenamento das mídias em URLs estáticas e expostas na web, sem uso de tokens de curta duração (URLs assinadas) ou proteção DRM. | 3 | 3 | **9** | **Alto** |
| **R17** | Info Disclosure (T17) | Atacantes enumeram a base de usuários ativos na plataforma para disparar campanhas direcionadas de phishing. | O sistema exibe mensagens de erro explícitas que revelam se o e-mail informado já está ou não cadastrado no banco de dados. | 4 | 1 | **4** | **Médio** |
| **R18** | Info Disclosure (T18) | Vazamento de sessões ativas, dados pessoais ou tokens de redefinição por meio de logs da aplicação. | Gravação inadvertida do corpo bruto das requisições HTTP (contendo payloads de senha e cabeçalhos de autenticação) nos arquivos de log. | 2 | 3 | **6** | **Médio** |
| **R19** | Denial of Service (T19) | Indisponibilidade de acesso e quedas no streaming de vídeo em momentos cruciais de aulas ao vivo. | Ausência de mecanismos de auto-scaling na infraestrutura e falta de distribuição de vídeo via rede de entrega de conteúdo (CDN). | 3 | 3 | **9** | **Alto** |
| **R20** | Denial of Service (T20) | Esgotamento de espaço de armazenamento e estouro financeiro de banda por uploads excessivos de arquivos pesados. | Falta de imposição de limite físico (quota de upload) de arquivos no servidor para perfis de instrutores. | 2 | 3 | **6** | **Médio** |
| **R21** | Denial of Service (T21) | Bloqueio em massa de contas legítimas de alunos e instrutores por ataques de força bruta direcionados. | Política de bloqueio rígido e de longa duração sobre nomes de usuário conhecidos, sem validação do IP de origem das falhas ou captcha. | 3 | 2 | **6** | **Médio** |
| **R22** | Elevation of Privilege (T22) | Aluno altera o papel (role) de seu próprio perfil de acesso no cliente para obter privilégios administrativos. | O servidor confia na claim de privilégio inserida no token JWT sem validar a assinatura criptográfica digital do segredo do token. | 2 | 4 | **8** | **Alto** |
| **R23** | Elevation of Privilege (T23) | Usuário autenticado acessa painéis administrativos ocultando botões apenas na interface, mas mantendo a API aberta. | Rotas administrativas do back-end não possuem middlewares de verificação de permissão baseada em perfis (RBAC), confiando apenas na ocultação de links na tela. | 3 | 4 | **12** | **Crítico** |
| **R24** | Elevation of Privilege (T24) | Atacante executa comandos remotos no servidor (RCE) e obtém controle completo do banco de dados e arquivos. | Utilização de dependências, frameworks e bibliotecas obsoletas de mercado que possuem vulnerabilidades públicas conhecidas (CVEs). | 2 | 4 | **8** | **Alto** |

---

## 2.5 Justificativa das Avaliações

As justificativas técnicas apresentadas a seguir detalham a análise lógica de probabilidade, impacto, ativos afetados e possíveis consequências para cada um dos 24 riscos operacionais mapeados para a plataforma Nexora. A classificação fundamenta-se nos critérios descritos nas Seções 2.1 e 2.2 deste relatório.

### R01 — Uso indevido de conta por credential stuffing

* **Origem e caso de abuso relacionado:** T01 — *Spoofing*; CA03 — Sequestro de conta de instrutor para desvio de repasses financeiros.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque a reutilização de senhas e a exposição de credenciais em outros serviços de internet tornam o ataque de *credential stuffing* altamente plausível contra a plataforma, facilitado pela ausência de mecanismos obrigatórios de segundo fator de autenticação (MFA) para todas as contas e de limites rígidos de tentativas de login (*rate limiting*).
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque o comprometimento de uma conta legítima expõe dados pessoais de alunos e instrutores, além de permitir o uso indevido de cursos ou a manipulação de dados cadastrais. No caso de perfis de instrutores, possibilita a tentativa de alteração de chaves de faturamento e desvio de repasses, gerando prejuízo operacional e financeiro relevante.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores, credenciais, sessões, dados pessoais, cursos, informações financeiras, serviço de autenticação e API Gateway.
* **Possíveis consequências e adequação do nível:** Invasão de conta, exposição de dados sob a LGPD, fraudes de faturamento e perda de credibilidade do serviço. O cálculo de probabilidade 3 × impacto 3 resulta em uma pontuação de 9 (Risco Alto), o que reflete a gravidade do cenário sem presumir o comprometimento sistêmico da infraestrutura em um único evento.

### R02 — Cadastro de instrutor falso

* **Origem e caso de abuso relacionado:** T02 — *Spoofing*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque, embora o cadastro de usuários seja aberto na plataforma, a publicação efetiva de cursos e a interação comercial exigem etapas adicionais de configuração. O abuso depende diretamente de falhas ou da ausência de processos internos de validação documental no momento do onboarding do profissional.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque a atuação de um instrutor falso pode lesar financeiramente os alunos, expor a plataforma a contestações de pagamento, violar direitos de propriedade intelectual de terceiros e causar danos significativos à imagem institucional e mercadológica da Nexora.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores legítimos, equipe de moderação, catálogo de cursos, reputação da marca e painel administrativo.
* **Possíveis consequências e adequação do nível:** Fraude comercial, desvalorização do catálogo, reembolso de valores e processos administrativos de suporte. O nível calculado de 6 (Risco Médio) é adequado, pois o impacto é severo para as vítimas diretas, mas o evento inicial pode ser isolado e mitigado por ações de moderação de conteúdo.

### R03 — Phishing com e-mails falsos

* **Origem e caso de abuso relacionado:** T03 — *Spoofing*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque o disparo de campanhas de phishing simulando comunicações oficiais da Nexora (como redefinição de senhas ou avisos de cursos) é um ataque comum de engenharia social, cuja execução é facilitada caso não existam assinaturas criptográficas e autenticações de DNS configuradas para o domínio.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque o ataque atinge diretamente a confiança dos usuários na comunicação da plataforma. Campanhas bem-sucedidas servem como vetor primário para o roubo massivo de credenciais de acesso, resultando em acessos não autorizados subsequentes.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores, domínio de e-mail corporativo, serviço de notificações e credenciais de acesso.
* **Possíveis consequências e adequação do nível:** Roubo de credenciais de acesso, vazamento de dados de perfis e incidentes de fraude. A pontuação de 9 (Risco Alto) é coerente com a alta plausibilidade de distribuição do ataque e a gravidade das consequências para os usuários afetados.

### R04 — Forjamento de callback de pagamento

* **Origem e caso de abuso relacionado:** T04 — *Spoofing*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque a exploração bem-sucedida requer que o atacante identifique a URL do endpoint de notificação e que a API do back-end falhe em validar criptograficamente a assinatura e a integridade da requisição HTTP POST recebida do gateway.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque a aceitação de notificações falsas resulta na liberação fraudulenta de conteúdos pagos sem a receita correspondente, gerando prejuízos financeiros diretos e inconsistências graves na conciliação contábil e no repasse financeiro aos instrutores.
* **Usuários, ativos e componentes afetados:** Equipe financeira, instrutores, API de pagamento, banco de dados e gateway de pagamento integrado.
* **Possíveis consequências e adequação do nível:** Perda de faturamento, divergências em relatórios fiscais, quebra de contratos de repasse com parceiros e consumo não autorizado de banda de streaming. A classificação em 6 (Risco Médio) reflete de forma precisa um cenário com impacto relevante, mas que depende de vulnerabilidade lógica específica na integração síncrona.

### R05 — Emissão de certificados por manipulação de progresso

* **Origem e caso de abuso relacionado:** T05 — *Tampering*; CA02 — Geração Fraudulenta de Certificado de Conclusão de Curso.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque a interceptação e modificação de requisições HTTP (com ferramentas de proxy local) é uma técnica trivial no desenvolvimento web, sendo de fácil execução se o servidor aceitar as atualizações de progresso enviadas pelo cliente de forma passiva, sem validar os tempos reais de reprodução no back-end.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque a geração de certificados inválidos compromete diretamente a reputação acadêmica e o modelo de negócios da Nexora, desvalorizando o selo institucional da startup no mercado de trabalho e perante parceiros educacionais.
* **Usuários, ativos e componentes afetados:** Alunos, registros acadêmicos (certificados e progresso), banco de dados e API de controle de progresso.
* **Possíveis consequências e adequação do nível:** Emissão de documentos fraudulentos, desvalorização de mercado dos cursos e quebra de critérios de conformidade acadêmica. A pontuação de 9 (Risco Alto) é adequada, pois atinge o principal entregável de valor do aluno na plataforma.

### R06 — Manipulação de preços ou cupons no cliente

* **Origem e caso de abuso relacionado:** T06 — *Tampering*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque a alteração de parâmetros e variáveis no lado do cliente (front-end) é um comportamento comum. Sem validação e recálculo obrigatório das tabelas de preços e cupons diretamente no servidor antes do envio ao gateway, o abuso torna-se viável.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque compras realizadas com valores adulterados geram prejuízos financeiros imediatos para a plataforma e impossibilitam a correta divisão de receitas e repasses tributários aos instrutores.
* **Usuários, ativos e componentes afetados:** Equipe financeira, instrutores, dados financeiros, API de checkout e gateway de pagamento.
* **Possíveis consequências e adequação do nível:** Perda direta de receita, quebra de confiança contratual com instrutores e distorção em relatórios fiscais. A pontuação de 9 (Risco Alto) justifica a necessidade mandatória de controles que retirem a lógica de cálculo do navegador do cliente.

### R07 — Alteração de notas de avaliações via API

* **Origem e caso de abuso relacionado:** T07 — *Tampering*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque a exploração exige que o usuário mapeie endpoints específicos de envio de notas e envie requisições estruturadas diretamente para a API de correção de avaliações, burlando a interface de provas do front-end.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque a adulteração direta de resultados acadêmicos invalida os critérios de avaliação pedagógica e compromete a integridade do processo de ensino-aprendizagem da plataforma.
* **Usuários, ativos e componentes afetados:** Alunos, registros acadêmicos (notas e avaliações), banco de dados e API de correção.
* **Possíveis consequências e adequação do nível:** Fraude acadêmica, distorção nas trilhas de aprendizado e aprovações indevidas. A classificação em 6 (Risco Médio) é condizente com o cenário de alteração pontual de dados lógicos que afeta a integridade, mas não causa a indisponibilidade do sistema.

### R08 — Edição e falsificação de arquivos de certificados (PDF)

* **Origem e caso de abuso relacionado:** T08 — *Tampering*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque a ausência de assinaturas digitais criptográficas e códigos de verificação pública no arquivo de certificado PDF gerado permite que qualquer indivíduo edite o arquivo de forma estática no computador local.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque a circulação de certificados falsificados contendo a marca e o nome da Nexora expõe a empresa a riscos jurídicos de falsidade documental e responsabilidade civil, além de anular a confiança do mercado no processo de certificação da plataforma.
* **Usuários, ativos e componentes afetados:** Alunos, registros acadêmicos, imagem institucional e repositório de arquivos estáticos.
* **Possíveis consequências e adequação do nível:** Processos jurídicos, fraudes curriculares e danos severos à imagem da marca. A pontuação de 9 (Risco Alto) reflete a necessidade crítica de mecanismos de validação criptográfica pública.

### R09 — Upload de materiais com malware

* **Origem e caso de abuso relacionado:** T09 — *Tampering*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque depende do comportamento de um instrutor legítimo mal-intencionado ou do comprometimento prévio de uma conta ativa de publicação de cursos para que o upload seja realizado diretamente na área de membros.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque a distribuição de arquivos contendo código malicioso (disfarçados de PDFs ou imagens de apoio didático) infectará as máquinas dos alunos que realizarem o download, gerando severo prejuízo aos usuários e responsabilização direta da plataforma.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores, conteúdo educacional, repositório de armazenamento de arquivos e servidores de aplicação.
* **Possíveis consequências e adequação do nível:** Infecção de dispositivos de clientes, perda de reputação técnica e risco de contágio de malware na infraestrutura de servidores da Nexora. O nível de 6 (Risco Médio) reflete a gravidade do cenário contida pela necessidade de privilégios para a inserção inicial dos arquivos.

### R10 — Manipulação de reputação de cursos

* **Origem e caso de abuso relacionado:** T10 — *Tampering*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque a automação de cadastros de perfis falsos de alunos para inflar avaliações de cursos próprios ou depreciar cursos de concorrentes é uma prática comum em marketplaces online, de fácil execução caso não existam regras rígidas de restrição de escrita.
* **Justificativa do impacto:** O impacto foi classificado como 2 (Moderado) porque, embora crie distorções mercadológicas no catálogo de cursos e prejudique a experiência de novos alunos, não expõe credenciais, dados pessoais regulados ou informações financeiras críticas.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores, catálogo de cursos, fórum e banco de dados.
* **Possíveis consequências e adequação do nível:** Concorrência desleal, distorção no catálogo de recomendações e perda de credibilidade das notas do catálogo. A classificação em 6 (Risco Médio) reflete adequadamente um impacto moderado associado a uma probabilidade média-alta de exploração.

### R11 — Manipulação de dados de gamificação

* **Origem e caso de abuso relacionado:** T11 — *Tampering*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque a alteração de pontuações e rankings requer que o usuário realize engenharia reversa no front-end para descobrir os endpoints específicos do serviço de gamificação e submeta requisições falsificadas que o servidor falhe em revalidar.
* **Justificativa do impacto:** O impacto foi classificado como 1 (Baixo) porque a gamificação na Nexora atua apenas como vetor de engajamento social. A adulteração dessas métricas causa pequenos transtornos de usabilidade técnica, mas não afeta funções vitais de faturamento, dados cadastrais ou mídias.
* **Usuários, ativos e componentes afetados:** Alunos, rankings públicos, algoritmos de recomendação e banco de dados.
* **Possíveis consequências e adequação do nível:** Distorções estéticas em rankings e perda de valor do fator de engajamento lúdico. O cálculo de probabilidade 2 × impacto 1 resulta em uma pontuação de 2 (Risco Baixo), justificando apenas o monitoramento periódico.

### R12 — Contestação indevida de compras (Chargeback)

* **Origem e caso de abuso relacionado:** T12 — *Repudiation*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque a prática de consumir o conteúdo do curso digital e, posteriormente, alegar desconhecimento da transação comercial junto à operadora do cartão de crédito (*friendly fraud*) é recorrente no mercado de infoprodutos.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque o excesso de contestações indevidas causa perda direta de receita e aplicação de taxas adicionais punitivas pelas adquirentes e gateways, podendo levar à suspensão temporária da conta financeira da Nexora.
* **Usuários, ativos e componentes afetados:** Equipe financeira, gateway de pagamento, dados financeiros e logs de auditoria.
* **Possíveis consequências e adequação do nível:** Perda de receita financeira, despesas de contencioso administrativo e multas financeiras. A classificação de 9 (Risco Alto) reflete a necessidade de implementar logs transacionais detalhados que sirvam de prova de consumo contra as contestações.

### R13 — Alteração de conteúdo de cursos por instrutor

* **Origem e caso de abuso relacionado:** T13 — *Repudiation*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque a remoção ou modificação abrupta de aulas vendidas por parte de instrutores é um evento que ocorre de forma isolada, tipicamente motivada por conflitos contratuais ou reorganização de material didático.
* **Justificativa do impacto:** O impacto foi classificado como 2 (Moderado) porque gera conflitos diretos de suporte ao cliente, reclamações de alunos que já haviam adquirido o curso e dificuldades de mediação por parte da plataforma caso não haja trilha de auditoria e versionamento de conteúdo.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores, equipe de suporte e banco de dados.
* **Possíveis consequências e adequação do nível:** Aumento de tickets de suporte, cancelamento de matrículas e reembolsos. A pontuação de 4 (Risco Médio) reflete com realismo uma inconsistência de conteúdo que pode ser contornada por regras administrativas.

### R14 — Ações nocivas de usuários privilegiados nos logs

* **Origem e caso de abuso relacionado:** T14 — *Repudiation*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque ações nocivas e subsequente apagamento ou alteração de logs administrativos dependem de um operador interno mal-intencionado ou de uma conta administrativa de alto nível severamente comprometida.
* **Justificativa do impacto:** O impacto foi classificado como 4 (Muito alto) porque a integridade dos logs de auditoria é a garantia técnica para a apuração de responsabilidades e investigação forense pós-incidente. Sua perda encobre fraudes, vazamentos de dados e abusos operacionais cometidos de forma interna.
* **Usuários, ativos e componentes afetados:** Administradores, logs de auditoria, banco de dados e painel administrativo.
* **Possíveis consequências e adequação do nível:** Perda de rastreabilidade, impunidade de ações nocivas internas e descumprimento de deveres legais de segurança. A classificação em 8 (Risco Alto) é adequada devido ao impacto severo, mesmo sob probabilidade moderada de ocorrência.

### R15 — Acesso não autorizado a dados pessoais via API (IDOR)

* **Origem e caso de abuso relacionado:** T15 — *Information Disclosure*; CA04 — Visualização Insegura de Perfis de Outros Alunos.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque falhas de Broken Object Level Authorization (BOLA/IDOR), em que parâmetros de requisição como identificadores de usuário (`id=X`) no navegador são alterados manualmente, representam uma das vulnerabilidades mais frequentes em APIs web modernas.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque a exploração dessa falha permite que qualquer usuário autenticado comum raspe e extraia em massa dados cadastrais privados (como e-mail e CPF) de toda a base de usuários da Nexora, violando diretamente a privacidade e as obrigações da LGPD.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores, dados pessoais, API Gateway e banco de dados.
* **Possíveis consequências e adequação do nível:** Vazamento massivo de informações, multas administrativas graves por descumprimento da LGPD e quebra irreparável de reputação comercial. A pontuação de 9 (Risco Alto) reflete a facilidade de exploração combinada ao rigor das consequências.

### R16 — Pirataria e download direto de videoaulas

* **Origem e caso de abuso relacionado:** T16 — *Information Disclosure*; CA01 — Download e Distribuição Não Autorizada de Videoaulas.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque, sem o uso de tokens dinâmicos temporários ou sistemas de proteção de mídia, qualquer usuário com acesso ao curso pode facilmente extrair a URL de origem estática dos vídeos usando as ferramentas nativas de desenvolvimento do navegador para realizar o download.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque a distribuição e a comercialização pirata de videoaulas pagas desvalorizam a plataforma comercial da startup e causam perdas financeiras diretas de propriedade intelectual dos instrutores, que podem abandonar o marketplace.
* **Usuários, ativos e componentes afetados:** Instrutores, alunos legítimos, conteúdo educacional e serviço de streaming.
* **Possíveis consequências e adequação do nível:** Perda de propriedade intelectual, pirataria sistêmica e quebra do ecossistema de venda de cursos. O nível calculado de 9 (Risco Alto) reflete com precisão o impacto direto ao coração do modelo de negócios da Nexora.

### R17 — Enumeração de e-mails ativos na autenticação

* **Origem e caso de abuso relacionado:** T17 — *Information Disclosure*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 4 (Alta) porque o fornecimento de mensagens de erro explícitas e diferenciadas para e-mails cadastrados e não cadastrados em telas de login e recuperação de senha é uma prática comum de usabilidade padrão, presente em sistemas sem arquitetura de segurança direcionada.
* **Justificativa do impacto:** O impacto foi classificado como 1 (Baixo) porque a listagem de e-mails existentes no sistema, isoladamente, não expõe senhas ou dados cadastrais sensíveis. Ela atua apenas como facilitador para que atacantes refinem listas de e-mails corporativos para tentativas direcionadas de força bruta e phishing.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores e serviço de autenticação.
* **Possíveis consequências e adequação do nível:** Criação de listas de phishing específicas direcionadas aos usuários do sistema. A pontuação calculada de 4 (Risco Médio) reflete de forma coerente a alta probabilidade do evento acoplada a uma gravidade de impacto insignificante para o sistema isoladamente.

### R18 — Registro de dados sensíveis em logs

* **Origem e caso de abuso relacionado:** T18 — *Information Disclosure*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque depende do comportamento de desenvolvedores que deixem habilitados logs de depuração verbosos em produção, registrando indevidamente o corpo bruto das requisições HTTP (com payloads de login contendo senhas em texto claro e chaves de acesso).
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque o armazenamento de credenciais e tokens em texto claro em arquivos de logs expõe diretamente essas chaves a administradores de banco de dados, operadores de suporte e analistas de infraestrutura, ampliando o risco de invasões internas de contas em cascata.
* **Usuários, ativos e componentes afetados:** Todos os usuários do sistema, credenciais, logs de auditoria e servidores de logs.
* **Possíveis consequências e adequação do nível:** Comprometimento interno de senhas, exposição inadequada de dados pessoais e infrações regulatórias. A pontuação de 6 (Risco Médio) é coerente por se tratar de um risco de segurança interno focado em falha operacional de configuração.

### R19 — Indisponibilidade de streaming nas aulas

* **Origem e caso de abuso relacionado:** T19 — *Denial of Service*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque as aulas ao vivo reúnem picos extremos e simultâneos de tráfego de usuários que podem sobrecarregar facilmente os servidores de API ou esgotar a banda disponível, especialmente se o streaming não estiver integrado a uma rede de entrega de conteúdo distribuído (CDN).
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque a queda do serviço de vídeo impede diretamente o consumo do principal produto comercializado pela Nexora, gerando desgaste imediato da marca, reclamações massivas e solicitações de estorno de compras.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores, disponibilidade da plataforma e serviço de streaming.
* **Possíveis consequências e adequação do nível:** Paralisação operacional das aulas, cancelamentos de assinaturas e danos de relações públicas. A pontuação de 9 (Risco Alto) reflete a criticidade da disponibilidade do streaming de vídeo para a startup.

### R20 — Esgotamento de storage por uploads excessivos

* **Origem e caso de abuso relacionado:** T20 — *Denial of Service*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque exige que um usuário legítimo com papel de instrutor, ou uma conta de instrutor invadida por terceiros, execute deliberadamente uploads repetidos e automatizados de arquivos de mídia excessivamente grandes sem que o back-end imponha limites físicos.
* **Justificativa do impacto:** O impacto foi classificado como 3 (Alto) porque o estouro de capacidade do armazenamento persistente impede que outros instrutores publiquem seus materiais legítimos, causa degradação no streaming global e gera uma elevação drástica e imprevisível de custos financeiros de infraestrutura de nuvem.
* **Usuários, ativos e componentes afetados:** Instrutores, equipe financeira, serviço de streaming e armazenamento de conteúdo.
* **Possíveis consequências e adequação do nível:** Paralisação de uploads, indisponibilidade parcial de infraestrutura e estouro de orçamento operacional em nuvem. A pontuação calculada de 6 (Risco Médio) é realista para um cenário de exaustão de recursos financeiros e físicos de armazenamento.

### R21 — Bloqueio malicioso de contas legítimas (Login)

* **Origem e caso de abuso relacionado:** T21 — *Denial of Service*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque um atacante que possua uma lista pública de e-mails de instrutores ou administradores da Nexora pode facilmente criar um script automatizado para enviar requisições falhas consecutivas de login para essas contas, disparando o bloqueio preventivo de segurança.
* **Justificativa do impacto:** O impacto foi classificado como 2 (Moderado) porque, embora crie transtornos de usabilidade técnica, indisponibilize o acesso legítimo temporariamente e sobrecarregue o canal de atendimento e suporte, o ataque não resulta em perda financeira definitiva, violação de LGPD ou alteração física de dados.
* **Usuários, ativos e componentes afetados:** Alunos, instrutores, administradores e serviço de autenticação.
* **Possíveis consequências e adequação do nível:** Bloqueio de acessos legítimos de usuários, frustração do cliente e congestionamento do suporte de TI. O nível calculado de 6 (Risco Médio) reflete com precisão uma falha de negação de serviço focada na perturbação da usabilidade.

### R22 — Elevação de privilégios por token JWT

* **Origem e caso de abuso relacionado:** T22 — *Elevation of Privilege*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque depende diretamente de uma falha grave na lógica de autenticação desenvolvida para o back-end, como o uso de chaves criptográficas fracas para assinar os tokens ou a ausência de verificação explícita no middleware do back-end sobre claims manipuladas de perfil.
* **Justificativa do impacto:** O impacto foi classificado como 4 (Muito alto) porque a aceitação de um token JWT manipulado pelo cliente com privilégios forjados de `Administrador` concede ao atacante acesso irrestrito às rotas administrativas, permitindo a exclusão de contas, acesso a relatórios financeiros e desvios operacionais.
* **Usuários, ativos e componentes afetados:** Administradores, perfis e permissões, banco de dados e serviço de autenticação.
* **Possíveis consequências e adequação do nível:** Elevação completa de privilégios, invasão de áreas internas restritas e perda de integridade lógica do banco de dados. A pontuação calculada de 8 (Risco Alto) reflete a severidade da falha lícita de autenticação.

### R23 — Acesso administrativo direto via API (Bypass)

* **Origem e caso de abuso relacionado:** T23 — *Elevation of Privilege*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 3 (Média-alta) porque é um erro comum e recorrente de desenvolvimento de APIs web omitir as validações de papéis de acesso (*RBAC*) nos endpoints e rotas do back-end, confiando de forma ingênua apenas na ocultação visual de links e botões na tela do navegador do cliente.
* **Justificativa do impacto:** O impacto foi classificado como 4 (Muito alto) porque um atacante com uma conta de nível básico (Aluno) pode burlar a interface e fazer chamadas de rede diretas para os endpoints administrativos, obtendo acesso de escrita a dados bancários, exclusão de registros e alteração de privilégios.
* **Usuários, ativos e componentes afetados:** Administradores, equipe financeira, perfis e permissões, painel administrativo e API Gateway.
* **Possíveis consequências e adequação do nível:** Comprometedora elevação de privilégios, manipulação arbitrária do back-end e evasão total de controles de autorização. O cálculo de probabilidade 3 × impacto 4 gera uma pontuação de 12 (Risco Crítico), o que justifica de forma sólida sua mitigação imediata e mandatória antes da liberação em ambiente de produção.

### R24 — Execução de comandos no servidor (RCE)

* **Origem e caso de abuso relacionado:** T24 — *Elevation of Privilege*.
* **Justificativa da probabilidade:** A probabilidade foi classificada como 2 (Média-baixa) porque a exploração bem-sucedida de falhas de Remote Code Execution (RCE) depende da existência de vulnerabilidades conhecidas (CVEs) ativas em frameworks ou bibliotecas de terceiros desatualizadas no servidor, acopladas à falta de análises estáticas no pipeline de deploy.
* **Justificativa do impacto:** O impacto foi classificado como 4 (Muito alto) porque a execução remota de comandos permite que um agente malicioso obtenha o controle completo do sistema operacional do servidor, permitindo o sequestro do banco de dados (ataque de Ransomware) e o comprometimento total da infraestrutura lógica da Nexora.
* **Usuários, ativos e componentes afetados:** Todos os usuários do sistema, banco de dados, servidores de aplicação, APIs e infraestrutura física e em nuvem.
* **Possíveis consequências e adequação do nível:** Sequestro de dados corporativos, paralisação sistêmica das operações e interrupção completa dos serviços web. A pontuação calculada de 8 (Risco Alto) é condizente com o impacto catastrófico do cenário sob probabilidade moderada.

---

## 2.6 Priorização dos Riscos

A tabela abaixo apresenta a classificação definitiva e a ordem de prioridade para o tratamento de todos os 24 riscos modelados para a plataforma Nexora. A ordenação considera a pontuação matemática, a criticidade dos ativos envolvidos (dados pessoais/financeiros), a escala de usuários afetados e a dificuldade de recuperação pós-incidente.

| Prioridade | ID | Risco | Pontuação | Nível | Ativo Crítico Afetado | Justificativa da Prioridade |
| :---: | :---: | :--- | :---: | :---: | :--- | :--- |
| **1** | **R23** | Acesso administrativo direto via API (Bypass de front-end) | **12** | **Crítico** | Perfis e Permissões / Painel Admin | Único risco classificado como **Crítico**. Sua exploração concede controle administrativo total do sistema de forma trivial, burlando todas as interfaces visuais. |
| **2** | **R15** | Acesso não autorizado a dados pessoais via API (IDOR) | **9** | **Alto** | Dados Pessoais (LGPD) | Vazamento massivo de dados pessoais (CPF, e-mails). Possui altíssimo risco de sanções jurídicas e multas administrativas severas pela ANPD (LGPD). |
| **3** | **R12** | Contestação indevida de compras consumidas (Chargeback) | **9** | **Alto** | Dados Financeiros / Receita | Fraude financeira direta e recorrente no modelo digital, ameaçando a própria conta mercantil da Nexora junto às adquirentes. |
| **4** | **R06** | Manipulação de preços ou cupons no cliente | **9** | **Alto** | Dados Financeiros / Receita | Causa prejuízo financeiro imediato nas compras de cursos e inconsistência grave nos repasses devidos aos instrutores. |
| **5** | **R01** | Uso indevido de conta por *credential stuffing* | **9** | **Alto** | Credenciais e Sessões | É o principal vetor ativo de sequestro de contas e serve de porta de entrada para fraudes financeiras e roubo de dados. |
| **6** | **R16** | Pirataria e download direto de videoaulas pagas | **9** | **Alto** | Conteúdo Educacional | Afeta a propriedade intelectual central que sustenta o modelo de negócios da startup, afastando instrutores parceiros. |
| **7** | **R19** | Indisponibilidade de streaming durante aulas ao vivo | **9** | **Alto** | Disponibilidade da Plataforma | Impacto simultâneo em massa sobre a base de alunos ativos, gerando pedidos de reembolso coletivos e danos severos de imagem. |
| **8** | **R05** | Emissão de certificados por manipulação de progresso | **9** | **Alto** | Registros Acadêmicos | Compromete a reputação e a integridade acadêmica do principal produto final entregue aos alunos da Nexora. |
| **9** | **R08** | Edição e falsificação de arquivos de certificados (PDF) | **9** | **Alto** | Registros Acadêmicos | Risco de falsidade documental e responsabilização civil/jurídica direta da plataforma Nexora no mercado. |
| **10** | **R03** | Phishing com e-mails falsos | **9** | **Alto** | Domínio e Notificações | Vetor de engenharia social de grande escala, embora dependa de uma ação ativa de interação de e-mail por parte da vítima. |
| **11** | **R24** | Execução de comandos no servidor por dependências (RCE) | **8** | **Alto** | Infraestrutura / Servidores | Embora tenha probabilidade média-baixa (2), o impacto de um RCE é devastador (4), podendo resultar no sequestro total de servidores (Ransomware). |
| **12** | **R22** | Elevação de privilégios por manipulação de token JWT | **8** | **Alto** | Perfis e Permissões | Permite que uma conta comum (Aluno) forje permissões do back-end, escalando privilégios diretamente na sessão ativa. |
| **13** | **R14** | Ações nocivas de usuários privilegiados sem logs | **8** | **Alto** | Logs de Auditoria | Impede investigações forenses internas, acobertando fraudes operacionais e vazamentos de dados causados por agentes internos. |
| **14** | **R04** | Forjamento de callback de pagamento | **6** | **Médio** | Gateway de Pagamento | Libera acessos a conteúdos pagos sem receita real correspondente, prejudicando o fluxo financeiro. |
| **15** | **R18** | Registro de dados sensíveis e credenciais em logs | **6** | **Médio** | Logs de Auditoria / Senhas | Expõe credenciais brutas de usuários em logs legíveis por equipes internas de TI, violando preceitos de privacidade por padrão. |
| **16** | **R02** | Cadastro de instrutor falso | **6** | **Médio** | Reputação da Plataforma | Pode lesar alunos pontualmente com cursos falsos, mas é facilmente mitigado por ações de denúncia e moderação manual. |
| **17** | **R09** | Upload de materiais com malware (PDF/Imagens) | **6** | **Médio** | Conteúdo Educacional / Alunos | Infecta dispositivos de alunos, porém o vetor de entrada é limitado por exigir perfis ativos de publicação de cursos. |
| **18** | **R20** | Esgotamento de storage por uploads excessivos | **6** | **Médio** | Armazenamento / Infraestrutura | Gera indisponibilidade de novos uploads e aumento repentino de custos, mas não paralisa o consumo de cursos existentes. |
| **19** | **R07** | Alteração de notas de avaliações via API | **6** | **Médio** | Registros Acadêmicos | Fraude localizada nas provas, de baixo alcance financeiro direto, passível de revisão manual de banco de dados. |
| **20** | **R21** | Bloqueio malicioso de contas por login incorreto | **6** | **Médio** | Serviço de Autenticação | Transtorno de usabilidade temporária de contas, sem vazamentos de dados ou perdas financeiras consolidadas. |
| **21** | **R10** | Manipulação de reputação de cursos | **6** | **Médio** | Fóruns e Avaliações | Distorce a relevância do catálogo e prejudica a concorrência honesta, sendo contornável com análises históricas. |
| **22** | **R13** | Alteração não rastreada de conteúdos de cursos vendidos | **4** | **Médio** | Conteúdo Educacional | Gera atrito entre instrutor e aluno, mas é resolvido internamente pelo suporte ao cliente da plataforma. |
| **23** | **R17** | Enumeração de e-mails ativos na autenticação | **4** | **Médio** | Serviço de Autenticação | Facilita a criação de listas de spam/phishing direcionadas, mas não expõe as senhas ou dados sensíveis dos perfis. |
| **24** | **R11** | Manipulação de dados de gamificação e conquistas | **2** | **Baixo** | Gamificação e Rankings | Puramente cosmético. O comprometimento do ranking de engajamento não traz danos operacionais ou financeiros à Nexora. |

### Justificativa da ordem provisória

A priorização seguiu uma lógica rígida baseada no impacto real ao ecossistema da Nexora:
1. **Riscos Críticos e Privilégios Gerais (Prioridade 1):** O risco **R23** lidera de forma absoluta por anular toda a estrutura de autorização da API com baixíssimo esforço técnico.
2. **Confidencialidade e LGPD (Prioridade 2):** A proteção de dados cadastrais (**R15**) sobrepõe-se a riscos financeiros por envolver danos jurídicos regulatórios em larga escala perante a ANPD.
3. **Integridade Financeira e Sobrevivência do Negócio (Prioridades 3 a 5):** Fraudes ativas de chargeback, manipulação direta de preços no carrinho e o credential stuffing ameaçam diretamente as operações comerciais de faturamento do software.
4. **Propriedade Intelectual e Operação Crítica (Prioridades 6 e 7):** A perda de mídias para pirataria e a queda do streaming síncrono ferem o núcleo de entrega de valor para instrutores e alunos.
5. **Integridade Acadêmica (Prioridades 8 e 9):** Certificados falsificados e progresso alterado minam a credibilidade da marca em longo prazo.
6. **Controles de Infraestrutura (Prioridades 11 a 13):** Embora graves, dependem de falhas sistêmicas de biblioteca ou do uso indevido de permissões, exigindo monitoramento de engenharia para mitigações secundárias.
7. **Riscos Médios e de Usabilidade (Prioridades 14 a 23):** Tratam-se de eventos isolados, fraudes de conciliação interna ou lentidões parciais que não comprometem a confidencialidade ou as chaves mestras de autenticação.
8. **Riscos de Baixo Impacto (Prioridade 24):** O sistema de gamificação destina-se apenas a engajamento cosmético e não possui interfaces financeiras ou acadêmicas críticas.

---

## 2.7 Estratégias de Tratamento
Definida a criticidade de cada risco, esta seção estabelece **como** cada um será tratado. O tratamento não se confunde com o controle técnico: primeiro o grupo decide a postura estratégica diante do risco (evitar, reduzir, compartilhar ou aceitar), e só depois seleciona as medidas concretas.

Foram consideradas as quatro estratégias clássicas de tratamento:

| Estratégia | Descrição | Quando é apropriada na Nexora |
| :--- | :--- | :--- |
| **Evitar** | Eliminar a atividade, funcionalidade ou condição que dá origem ao risco. | Somente quando a funcionalidade não for essencial ao negócio, o que raramente ocorre em uma plataforma cuja operação depende de cadastro aberto, pagamentos e streaming. |
| **Reduzir** | Implementar controles que diminuam a probabilidade de ocorrência, o impacto, ou ambos. | Estratégia predominante, aplicável quando a funcionalidade precisa continuar existindo, mas pode receber salvaguardas adicionais. |
| **Compartilhar** | Atribuir parte da operação, da execução do controle ou de suas consequências a um terceiro. | Aplicável às integrações externas da Nexora — gateway de pagamento, provedor de e-mail, serviço de streaming e infraestrutura em nuvem. |
| **Aceitar** | Reconhecer e manter conscientemente o risco, com justificativa formal e acompanhamento periódico. | Aplicável ao risco residual que permanece após o tratamento, quando o custo de reduzi-lo ainda mais for desproporcional ao benefício. |

### 2.7.1 Estratégia escolhida para cada risco
Esta tabela apresenta o mapeamento completo e individualizado das posturas de tratamento adotadas para cada evento de risco da plataforma Nexora.

| Risco | Nível inicial | Estratégia principal | Justificativa da escolha |
| :--- | :---: | :--- | :--- |
| **R01** | Alto (9) | **Reduzir** | O acesso autenticado é vital para a plataforma. A probabilidade do risco é reduzida com implementação de MFA e rate limiting. |
| **R02** | Médio (6) | **Reduzir** | O cadastro de instrutores é a base do marketplace de cursos. Reduzimos o risco por meio de validação manual no cadastro. |
| **R03** | Alto (9) | **Reduzir e Compartilhar** | Configurações DNS (SPF, DKIM, DMARC) reduzem o spoofing, enquanto a infraestrutura de entrega das mensagens é compartilhada com o provedor de e-mail contratado. |
| **R04** | Médio (6) | **Reduzir e Compartilhar** | A validação HMAC no back-end reduz o risco de fraude, enquanto o processamento financeiro e a geração segura das assinaturas pertencem ao gateway de pagamento parceiro. |
| **R05** | Alto (9) | **Reduzir** | A progressão acadêmica de aulas não pode ser removida. O risco de trapaça é reduzido com regras de validação síncronas de tempo assistido no servidor. |
| **R06** | Alto (9) | **Reduzir** | A compra com cupons e preços dinâmicos é necessária para o faturamento. O tratamento foca em recalcular e validar todos os valores estritamente no back-end. |
| **R07** | Médio (6) | **Reduzir** | Avaliações de alunos são essenciais para aprovação. Reduzimos o risco implementando verificações rígidas de autoria de provas no servidor. |
| **R08** | Alto (9) | **Reduzir** | O fornecimento de certificados em PDF é uma funcionalidade essencial. O tratamento consiste em adicionar assinaturas digitais criptográficas e chaves de validação pública. |
| **R09** | Médio (6) | **Reduzir** | O upload de arquivos de apoio didático pelos instrutores é indispensável. Reduzimos o risco com análise de arquivos e validação de tipo MIME. |
| **R10** | Médio (6) | **Reduzir** | Fóruns e avaliações de reputação geram engajamento. A probabilidade de manipulação é reduzida aplicando regras de negócios que limitam notas a quem assistiu às aulas. |
| **R11** | Baixo (2) | **Reduzir e Aceitar residual** | A gamificação impulsiona o uso, mas possui baixo impacto comercial. Reduzimos a exposição na API de conquistas e aceitamos o risco residual estético. |
| **R12** | Alto (9) | **Reduzir e Aceitar residual** | Vendas online estão sempre sujeitas a chargebacks de má-fé. Reduzimos o risco colhendo metadados robustos de auditoria e aceitamos o risco residual comercial inerente. |
| **R13** | Médio (4) | **Reduzir** | A edição de cursos por instrutores é necessária. Mitiga-se o risco através de versionamento automático de conteúdo e geração automática de logs de alterações. |
| **R14** | Alto (8) | **Reduzir** | Operadores precisam de privilégios para suporte. Reduzimos o abuso enviando logs administrativos em tempo real para um servidor isolado. |
| **R15** | Alto (9) | **Reduzir** | A visualização de perfis públicos no fórum é necessária para a interação social. Reduzimos o vazamento via validação de autorização de nível de objeto na API. |
| **R16** | Alto (9) | **Reduzir** | O consumo de videoaulas é o coração do serviço. Reduzimos a pirataria com URLs assinadas e controle rígido de sessão de streaming. |
| **R17** | Médio (4) | **Reduzir** | O fluxo de recuperação e login deve existir. Reduzimos a enumeração unificando e padronizando todas as mensagens de erro de autenticação. |
| **R18** | Médio (6) | **Reduzir** | A gravação de logs é obrigatória para conformidade. O risco é reduzido configurando middlewares de mascaramento automático de dados sensíveis. |
| **R19** | Alto (9) | **Reduzir e Compartilhar** | O streaming de aulas precisa ocorrer sob alta demanda. A Nexora implementa cache e tratamento locais, compartilhando a distribuição de banda pesada com uma CDN. |
| **R20** | Médio (6) | **Reduzir** | O armazenamento persistente de arquivos é fundamental. Reduzimos o abuso aplicando limites físicos de cota de disco por instrutor. |
| **R21** | Médio (6) | **Reduzir** | O bloqueio preventivo de login evita invasões. Reduzimos o DoS contra usuários legítimos usando recaptcha e bloqueando IPs suspeitos, em vez de contas. |
| **R22** | Alto (8) | **Reduzir** | A autenticação por token JWT é a escolha de arquitetura do sistema. Reduzimos o risco exigindo validação criptográfica estrita das assinaturas dos tokens no servidor. |
| **R23** | Crítico (12) | **Reduzir** | O painel administrativo é indispensável para a operação do negócio. O risco crítico é reduzido aplicando middlewares centralizados de autorização RBAC nas APIs. |
| **R24** | Alto (8) | **Reduzir** | O uso de bibliotecas de terceiros acelera o desenvolvimento. Reduzimos o risco de vulnerabilidades ativas inserindo checagens SCA automatizadas no pipeline. |

### 2.7.2 Observações sobre as estratégias não adotadas

**Nenhum risco recebeu a estratégia Evitar de forma integral.** A eliminação de qualquer uma das atividades geradoras de risco significaria extinguir os pilares básicos da plataforma Nexora: cadastro público, comercialização de cursos, visualização de videoaulas, fórum social e emissão de certificados. Como a redução por controles técnicos e organizacionais é perfeitamente viável em todos os casos, a supressão dessas funcionalidades de negócio foi descartada de forma consciente.

**A estratégia Aceitar não foi adotada de forma isolada em nenhum risco alto ou crítico.** Ela foi aplicada exclusivamente de forma parcial ao risco residual que permanece após a implementação de defesas (como nos riscos comerciais e funcionais secundários de chargeback **R12** e gamificação **R11**). Aceitar integralmente um risco de nível alto ou crítico antes do tratamento violaria os critérios básicos de conformidade da LGPD e as políticas de integridade financeira do projeto.

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
A tabela cruzada abaixo indica quais funções do NIST CSF 2.0 são prioritárias e devem ser consideradas no tratamento de cada um dos 24 riscos identificados no ecossistema da Nexora. A marcação com "X" significa que a função produz um resultado relevante e essencial para mitigar ou gerenciar aquele risco específico.

| Risco | Govern | Identify | Protect | Detect | Respond | Recover |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **R01 — Uso indevido de conta** | X | X | X | X | X | X |
| **R02 — Cadastro de instrutor falso** | X | X | X | X | X | X |
| **R03 — Phishing com e-mails falsos** | X | X | X | X | X | — |
| **R04 — Forjamento de callback de pagamento** | X | X | X | X | X | X |
| **R05 — Emissão de certificados por manipulação de progresso** | X | X | X | X | X | X |
| **R06 — Manipulação de preços ou cupons no cliente** | X | X | X | X | X | X |
| **R07 — Alteração de notas de avaliações via API** | X | X | X | X | X | X |
| **R08 — Edição e falsificação de certificados (PDF)** | X | X | X | X | X | X |
| **R09 — Upload de materiais com malware** | X | X | X | X | X | X |
| **R10 — Manipulação de reputação de cursos** | X | X | X | X | X | X |
| **R11 — Manipulação de dados de gamificação** | X | X | X | X | X | X |
| **R12 — Contestação indevida de compras (Chargeback)** | X | — | X | X | X | — |
| **R13 — Alteração de conteúdo de cursos por instrutor** | X | — | X | X | X | — |
| **R14 — Ações nocivas de usuários privilegiados** | X | — | X | X | X | — |
| **R15 — Acesso não autorizado a dados pessoais (IDOR)** | X | X | X | X | X | X |
| **R16 — Pirataria e download direto de videoaulas** | X | X | X | X | X | — |
| **R17 — Enumeração de e-mails ativos na autenticação** | X | X | X | X | X | — |
| **R18 — Registro de dados sensíveis em logs** | X | X | X | X | X | X |
| **R19 — Indisponibilidade de streaming nas aulas** | X | X | X | X | X | X |
| **R20 — Esgotamento de storage por uploads excessivos** | X | X | X | X | X | X |
| **R21 — Bloqueio malicioso de contas legítimas (Login)** | X | X | X | X | X | X |
| **R22 — Elevação de privilégios por token JWT** | X | X | X | X | X | X |
| **R23 — Acesso administrativo direto via API (Bypass)** | X | X | X | X | X | X |
| **R24 — Execução de comandos no servidor (RCE)** | X | X | X | X | X | X |

### 2.9.1 Justificativa das lacunas do mapeamento

Para manter o rigor analítico exigido e evitar o preenchimento automático e irrefletido de todas as células, algumas ausências de marcação de função foram mantidas de forma deliberada e justificada:

*   **R03, R16 e R17 — Função *Recover* não marcada:** 
    *   No **phishing (R03)**, o ataque ocorre externamente contra o dispositivo do usuário; não há ativos internos da Nexora danificados ou indisponíveis para recuperar. 
    *   Na **pirataria (R16)** e na **enumeração de e-mails (R17)**, uma vez que a informação foi copiada ou exposta para o atacante fora do limite de confiança da plataforma, não há estado de sistema técnico a ser restaurado. O foco reside na prevenção técnica (*Protect*) e no bloqueio de abuso (*Detect/Respond*).
*   **R12, R13 e R14 — Função *Identify* não marcada:** A negação de autoria em ações de repúdio não decorre de um ativo desconhecido ou de uma dependência sistêmica não documentada, mas sim da insuficiência dos logs produzidos sobre ações conhecidas. O foco estratégico está em definir o escopo de logs administrativos e financeiros (*Govern*), assegurar sua integridade (*Protect*), auditá-los (*Detect*) e apurar responsabilidades (*Respond*).
*   **R12, R13 e R14 — Função *Recover* não marcada:** Trilhas de auditoria dão suporte técnico para provar a autoria de uma ação, mas não restauram serviços diretamente. Se uma contestação revelar alterações indevidas em cursos ou cadastros que precisem ser revertidos, o restabelecimento do estado anterior pertence à função de recuperação dos respectivos riscos de integridade (como *Tampering* — R05, R06 ou R07) e não ao ato de resolver o repúdio em si.

### 2.9.2 Leitura do mapeamento

A distribuição dos "X" reforça a necessidade de **mecanismos de autorização centralizados** na plataforma Nexora:

1.  **Governança Obrigatória (Govern):** Todos os 24 riscos exigem governança ativa para estabelecer regras sobre quem decide níveis de aceitação de perdas e com qual periodicidade as credenciais ou privilégios de acesso serão revisados.
2.  **Proteção e Detecção como Pilares (Protect e Detect):** Como a maior parte das falhas da Nexora envolve dados recebidos do cliente atravessando os limites de confiança da API Gateway, blindar as entradas contra dados inválidos (*Protect*) e registrar as anomalias lógicas (*Detect*) são ações indispensáveis.
3.  **Restauração Crítica (Recover):** Riscos críticos de infraestrutura (como R24 — RCE ou R19 — DoS) e manipulação lógica de dados (como R15 — IDOR ou R22 — Elevação de privilégio) exigem procedimentos rápidos de redefinição de segurança, restauração de chaves e backups íntegros de banco de dados para desfazer os danos causados por uma invasão.

---

## 2.10 Plano de Tratamento
Após a análise e classificação dos riscos operacionais identificados na plataforma Nexora, foi estruturado o plano de tratamento contendo as contramedidas específicas, responsabilidades técnicas e as evidências observáveis para confirmar que cada controle existe e funciona de acordo com os preceitos do NIST CSF 2.0.

| Risco | Estratégia | Controles Propostos | Funções do NIST CSF | Responsáveis | Evidências e Verificação |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **R01** | Reduzir | 1. Implementação de Autenticação Multifator (MFA) obrigatória para contas administrativas e de Instrutores.<br>2. Mecanismo de limite de tentativas de login (Rate Limiting) no servidor de autenticação.<br>3. Política de senhas fortes validada diretamente no servidor. | Protect, Detect, Respond | Equipe de Desenvolvimento e Infraestrutura | 1. Registro de testes comprovando o bloqueio de requisições após tentativas consecutivas inválidas.<br>2. Logs de auditoria registrando acessos suspeitos.<br>3. Fluxo de verificação de token MFA ativo em ambiente de testes. |
| **R02** | Reduzir | 1. Processo de validação de identidade (homologação manual) pela equipe de moderação antes de permitir a publicação de cursos.<br>2. Envio obrigatório de comprovação de documentos e portfólio no painel administrativo. | Govern, Protect | Equipe de Moderação e Gestão de Negócios | 1. Registro no banco de dados mostrando a alteração do status da conta após validação humana.<br>2. Pasta de armazenamento seguro contendo as cópias das documentações apresentadas. |
| **R03** | Reduzir | 1. Configuração e publicação das diretivas SPF (Sender Policy Framework), DKIM (DomainKeys Identified Mail) e DMARC (Domain-based Message Authentication) com política de rejeição no DNS do domínio. | Protect, Detect | Equipe de Infraestrutura e Redes | 1. Consulta pública ao DNS provando a existência e validade dos registros SPF, DKIM e DMARC do domínio da Nexora.<br>2. Relatórios de falha de entrega de e-mails de servidores não autorizados. |
| **R04** | Reduzir | 1. Validação de assinaturas criptográficas (HMAC-SHA256) em todas as requisições enviadas pelo gateway de pagamento.<br>2. Implementação de consulta ativa reversa à API oficial do gateway para revalidação do status da compra antes de liberar o curso. | Protect, Detect, Respond | Equipe de Desenvolvimento | 1. Relatório de testes automatizados simulando retornos sem assinatura e comprovando a rejeição pelo servidor.<br>2. Logs do sistema exibindo tentativas de callback sem token ou assinatura válidos sendo bloqueadas. |
| **R05** | Reduzir | 1. Validação síncrona no back-end do tempo assistido das videoaulas por meio de requisições periódicas (*heartbeat/ping* de 30s) geradas de forma automática pelo player de vídeo.<br>2. Bloqueio lógico que impede a API de gerar o certificado se o percentual assistido salvo no banco não atingir 100% da trilha. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento | 1. Suíte de testes de integração simulando requisição HTTP de emissão de certificado com tempo de progresso zerado no banco, comprovando a rejeição pelo back-end. |
| **R06** | Reduzir | 1. O servidor de back-end desconsidera qualquer valor monetário ou preço enviado pelo cliente/navegador no ato da compra.<br>2. Recálculo obrigatório e validação de cupons de desconto executados estritamente do lado do servidor no momento da geração do checkout. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento e Equipe Financeira | 1. Teste de penetração automatizado alterando o preço de um curso no payload de requisição e comprovando que o gateway cobrou o valor correto recalculado pelo back-end. |
| **R07** | Reduzir | 1. Verificação de propriedade e estado da prova no back-end, cruzando o token JWT do aluno ativo com as matrículas existentes.<br>2. Restrição única no banco de dados para a tupla `(aluno_id, avaliacao_id)` para impedir reenvios de notas ou trapaças após a correção inicial. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento | 1. Teste de unidade enviando respostas duplicadas para o endpoint de correção e verificando o retorno HTTP 400 Bad Request.<br>2. Logs registrando tentativas de reenvio de avaliações concluídas. |
| **R08** | Reduzir | 1. Geração de código hash de validação criptográfica (SHA-256) baseado nos metadados do certificado (Nome, CPF, Curso e Carga Horária) e gravado de forma síncrona no banco de dados.<br>2. Disponibilização de um endpoint de verificação pública (`/validar-certificado`) no portal para conferência de autenticidade por terceiros. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento e Registros Acadêmicos | 1. Tela de validação de certificados funcional em ambiente de homologação rejeitando hashes alterados ou inexistentes no banco.<br>2. Validação visual do PDF contendo o QR Code direcionando ao endpoint seguro. |
| **R09** | Reduzir | 1. Validação do tipo real do arquivo carregado via verificação de assinatura de cabeçalho mágico (*Magic Bytes/MIME*) no servidor de upload (rejeitando bypass baseado apenas em renomeação de extensão).<br>2. Integração com biblioteca ClamAV via API para escaneamento ativo de vírus antes de salvar arquivos no storage. | Protect, Detect, Respond, Recover | Equipe de Infraestrutura e Desenvolvimento | 1. Teste de upload de um arquivo contendo a string de assinatura inofensiva de vírus EICAR, comprovando a rejeição imediata (HTTP 400) e a exclusão automática do arquivo temporário. |
| **R10** | Reduzir | 1. Middleware na API de avaliação que valida se o autor do comentário/nota possui matrícula ativa no curso e completou ao menos 15% de progresso assistido.<br>2. Regra de unicidade de nota por curso para impedir avaliações múltiplas de uma mesma conta de estudante. | Govern, Protect, Detect, Respond, Recover | Equipe de Desenvolvimento e Moderação | 1. Tentativa de requisição de POST em avaliações de cursos sem matrícula resultando em erro HTTP 403 Forbidden nos testes automatizados.<br>2. Logs registrando bloqueio de duplicidade. |
| **R11** | Reduzir e Aceitar residual | 1. Processamento e cálculo de conquistas e pontuações de forma assíncrona baseados em gatilhos de eventos gerados e validados no back-end (ex: conclusão de módulo confirmada), sem endpoints abertos que aceitem incremento direto de score pelo front-end. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento | 1. Inspeção de rotas da API em ambiente de desenvolvimento garantindo a ausência de endpoints do tipo `POST /api/v1/user/score` que aceitem valores inteiros arbitrários. |
| **R12** | Reduzir e Aceitar residual | 1. Registro de logs de auditoria transacional estruturado contendo IP de origem, geolocalização aproximada por IP, *fingerprint* do dispositivo do navegador e gravação em banco de dados do timestamp exato de aceite dos termos. | Govern, Protect, Detect, Respond | Equipe de Desenvolvimento e Equipe Financeira | 1. Relatório consolidado contendo os metadados de consumo de aulas e rastreabilidade da compra extraído para envio às adquirentes e operadoras em contestações de chargeback. |
| **R13** | Reduzir | 1. Versionamento histórico automático de conteúdo e grade de cursos no banco de dados. Qualquer alteração ou exclusão cria uma nova linha com `timestamp` e `instrutor_id`, mantendo a versão anterior inativa, mas armazenada para auditoria. | Govern, Protect, Detect, Respond | Equipe de Desenvolvimento e Moderação | 1. Verificação em ambiente de homologação comprovando que a exclusão de uma aula pelo instrutor não remove a linha física do banco, mas altera seu status de visibilidade. |
| **R14** | Reduzir | 1. Envio em tempo real dos logs do painel administrativo por protocolo syslog cifrado para um servidor central de logs separado e isolado (com política de gravação sem permissão de exclusão para administradores comuns). | Govern, Protect, Detect, Respond | Equipe de Infraestrutura e Redes | 1. Logs de atividades administrativas replicados com sucesso no servidor Syslog isolado.<br>2. Teste de tentativa de apagamento de log pelo console de administrador falhando por falta de privilégio de SSH no storage de logs. |
| **R15** | Reduzir | 1. Implementação de middleware de autorização baseado em propriedade de recurso (*RBAC/ABAC*) no back-end. A API de perfil `/api/v1/users/perfil` deve verificar se o ID requisitado corresponde ao ID extraído do token JWT autenticado (exceto se o solicitante for Administrador). | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento | 1. Execução de requisição com JWT do Aluno A tentando ler o perfil do Aluno B, comprovando o retorno de erro HTTP 403 Forbidden. |
| **R16** | Reduzir | 1. Geração de URLs de mídia assinadas criptograficamente e temporárias (expiração automática em 15 minutos) para carregamento do streaming no player de vídeo.<br>2. Transmissão segmentada do conteúdo via protocolo HLS com descriptografia em tempo real. | Protect, Detect, Respond | Equipe de Desenvolvimento e Infraestrutura | 1. Teste de download de vídeo utilizando ferramentas de desenvolvedor do navegador comprovando que links diretos ao vídeo expiram e retornam erro HTTP 403 Forbidden sem chaves ativas. |
| **R17** | Reduzir | 1. Unificação das mensagens de erro e respostas na tela de autenticação e fluxos de recuperação de senha. Em qualquer caso de falha de login, retornar mensagem genérica: "E-mail ou senha incorretos" com status HTTP 401. | Protect, Detect, Respond | Equipe de Desenvolvimento | 1. Testes automatizados de requisição comparando tempos de resposta de tentativas de login com e-mail existente e inexistente, confirmando que são equivalentes e retornam o mesmo payload JSON. |
| **R18** | Reduzir | 1. Middleware de higienização de strings em logs no back-end que substitui de forma sistemática chaves sensíveis em payloads HTTP (como "password", "token", "cpf") por máscaras do tipo `[REDACTED]` antes de gravar o evento no arquivo. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento | 1. Auditoria visual de logs de requisições de login gerados em ambiente de testes, verificando que os campos de senhas brutas estão sanitizados. |
| **R19** | Reduzir e Compartilhar | 1. Utilização de infraestrutura baseada em nuvem com distribuição de carga geográfica e cache das mídias de vídeo através de uma CDN global.<br>2. Configuração de regras de escalonamento automático (*Auto-scaling*) ativadas a partir de 75% de CPU nos servidores de API. | Protect, Detect, Respond, Recover | Equipe de Infraestrutura e DevOps | 1. Logs de simulação de testes de estresse de carga e concorrência comprovando o provisionamento automático de novos contêineres e distribuição de tráfego pela CDN sem queda de conexões. |
| **R20** | Reduzir | 1. Aplicação de quotas máximas de armazenamento físicas no back-end (ex: limite de 50GB por instrutor no S3) e limitação de tamanho de upload na API de vídeo para no máximo 500MB por arquivo de aula. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento e Infraestrutura | 1. Teste de upload de vídeo de 600MB rejeitado pelo middleware de validação do back-end com erro HTTP 413 Payload Too Large. |
| **R21** | Reduzir | 1. Substituição de política de bloqueio rígido de login por verificação de CAPTCHA (hCaptcha ou reCAPTCHA) após 3 tentativas inválidas consecutivas de login de um mesmo usuário, preservando o acesso legítimo de outras origens. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento | 1. Front-end exibindo desafio de CAPTCHA após terceira falha.<br>2. Usuário legítimo logando com sucesso via outro dispositivo sem ser afetado por tentativas consecutivas falhas vindas de IP atacante. |
| **R22** | Reduzir | 1. Validação criptográfica do token JWT no servidor baseada no algoritmo assimétrico RS256 com chave pública local.<br>2. Bloqueio explícito e rejeição no middleware de autenticação de tokens que declarem algoritmo `alg: none`. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento e Infraestrutura | 1. Suíte de testes automáticos simulando envio de token JWT contendo `alg: none` ou assinado com chave privada falsa, confirmando a rejeição e o retorno de HTTP 401 Unauthorized. |
| **R23** | Reduzir | 1. Middleware centralizado de autorização baseada em papéis (*RBAC*) injetado globalmente nas rotas de controle e endpoints administrativos (ex: `/api/v1/admin/*`), exigindo a claim `perfil = Administrador` no JWT. | Protect, Detect, Respond, Recover | Equipe de Desenvolvimento | 1. Testes automatizados de integração enviando requisição para endpoints administrativos logado com token de perfil Aluno, gerando HTTP 403 Forbidden. |
| **R24** | Reduzir | 1. Inclusão de varredura automatizada de Software Composition Analysis (*SCA*, ex: Snyk) integrada ao pipeline de integração contínua (CI/CD) para bloquear deploys caso dependências de bibliotecas de terceiros contenham vulnerabilidades críticas ou altas de RCE. | Protect, Detect, Respond, Recover | Equipe de DevOps e Segurança da Informação | 1. Logs de build do pipeline de desenvolvimento comprovando a interrupção do deploy quando dependências obsoletas contendo CVEs ativas são identificadas. |

---

## 2.11 Ordem Inicial de Implementação
A implantação das contramedidas de segurança da plataforma Nexora foi organizada em um cronograma técnico dividido em **seis fases lógicas**. Esta ordenação é estratégica e prioriza a resolução de riscos críticos e altos, respeitando as dependências técnicas da infraestrutura e otimizando o esforço da equipe de desenvolvimento.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ FASE 1: Infraestrutura Básica e Segurança de DNS (Quick Wins)               │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ FASE 2: Fortalecimento da Autenticação e Proteção do Fluxo de Login         │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ FASE 3: Controles de Autorização e Defesa de APIs Core (Riscos Críticos)    │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ FASE 4: Segurança de Mídias, Storage e Emissão de Certificados              │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ FASE 5: Segurança Transacional e Integrações Financeiras                    │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│ FASE 6: Logs de Auditoria, Governança e Processos Operacionais              │
└─────────────────────────────────────────────────────────────────────────────┘
```

#### Fase 1: Infraestrutura Básica e Segurança de DNS (Quick Wins)
Fase voltada para controles de baixíssima complexidade de código, mas de altíssimo impacto imediato na proteção de marca e na confiabilidade do ambiente.
*   **Controles Implementados:** 
    *   Configuração de diretivas SPF, DKIM e DMARC (`p=reject`) no DNS corporativo (**R03**).
    *   Inclusão de análise estática de segurança (SCA/SAST) integrada ao pipeline de CI/CD para detecção de dependências vulneráveis (**R24**).
*   **Justificativa Técnica:** As configurações de DNS são executadas diretamente nos painéis de registro de domínio, exigindo zero alteração no código do software Nexora. A implementação do SCA garante que nenhuma nova biblioteca vulnerável seja inserida nas fases seguintes.

#### Fase 2: Fortalecimento da Autenticação e Proteção do Fluxo de Login
Proteção do principal ponto de entrada de usuários e estabelecimento de chaves de identidade seguras, servindo como base técnica para as fases de autorização.
*   **Controles Implementados:**
    *   MFA obrigatório (TOTP) para administradores e instrutores, acompanhado de políticas de complexidade de senha e rate limiting no login (**R01**).
    *   Implementação de desafio CAPTCHA para evitar bloqueio malicioso de usuários legítimos (**R21**).
    *   Padronização de respostas genéricas de falha de autenticação para evitar enumeração de contas (**R17**).
    *   Validação criptográfica rígida de tokens JWT no back-end, rejeitando o algoritmo `none` (**R22**).
*   **Justificativa Técnica:** A autenticação forte e a validação do token JWT (**R22**) são requisitos de infraestrutura indispensáveis. É impossível construir um controle de autorização seguro (Fase 3) se a validação da identidade do remetente for frágil ou passível de falsificação.

#### Fase 3: Controles de Autorização e Defesa de APIs Core (Riscos Críticos)
Fase destinada a mitigar os abusos lógicos de maior pontuação do projeto (incluindo o risco crítico R23 e o risco alto R15), nos quais dados enviados pelo cliente são manipulados para burlar regras de negócio.
*   **Controles Implementados:**
    *   Middleware centralizado de autorização baseada em papéis (*RBAC*) injetado globalmente nas rotas administrativas da API (**R23**).
    *   Middleware de autorização baseada em propriedade de recurso (*ABAC/BOLA*) para proteger dados pessoais de perfis de vazamento de identificadores (**R15**).
    *   Validação síncrona no servidor para tempo de visualização de aulas, progresso e notas de avaliações (**R05**, **R07** e **R10**).
    *   Recálculo obrigatório e validação de cupons de desconto no back-end (**R06**).
*   **Justificativa Técnica:** Combate diretamente as maiores vulnerabilidades de lógica de negócios da plataforma. Exige que a validação de JWT (Fase 2) esteja ativa para extrair de forma segura as claims de perfil (`Administrador`) ou propriedade (`id_usuario`) no back-end.

#### Fase 4: Segurança de Mídias, Storage e Emissão de Certificados
Blindagem do core funcional de entrega de arquivos da plataforma (videoaulas de propriedade dos instrutores e PDFs acadêmicos oficiais).
*   **Controles Implementados:**
    *   Geração de URLs assinadas de curta duração e streaming segmentado via protocolo HLS (**R16**).
    *   Validação de assinaturas de cabeçalho mágico (*Magic Bytes/MIME*) e varredura ativa de vírus via ClamAV em uploads de materiais de apoio (**R09**).
    *   Aplicação de limites físicos de tamanho de arquivo e quotas de armazenamento por conta de instrutor (**R20**).
    *   Criptografia de metadados e validação pública de hashes de certificados (**R08**).
    *   Geração assíncrona de conquistas e pontuações lógicas de gamificação no back-end (**R11**).
*   **Justificativa Técnica:** Protege a propriedade intelectual dos cursos (que atrai e retém instrutores) e garante que o sistema de armazenamento não seja paralisado por abusos de upload ou arquivos maliciosos.

#### Fase 5: Segurança Transacional e Integrações Financeiras
Controles de integração com sistemas terceiros para fechamento seguro do ciclo de vendas e mitigação de perdas financeiras operacionais.
*   **Controles Implementados:**
    *   Validação síncrona de assinatura HMAC de callbacks recebidos do gateway de pagamento (**R04**).
    *   Registro de metadados transacionais detalhados (*fingerprint*, IP, geolocalização e consentimento de termos) para envio às adquirentes em disputas de chargeback (**R12**).
*   **Justificativa Técnica:** Garante que a liberação de matrículas pagas ocorra sem fraudes lógicas de simulação de compra. É implementada na reta final por necessitar de ambiente de homologação e credenciais de teste ativas junto ao gateway de pagamento (Iugu ou Stripe).

#### Fase 6: Logs de Auditoria, Governança e Processos Operacionais
Fase voltada para conformidade legal, monitoramento contínuo pós-deploy e rotinas administrativas manuais que apoiam a resiliência do sistema.
*   **Controles Implementados:**
    *   Centralização e replicação de logs do painel administrativo por meio de syslog cifrado para servidor isolado (**R14**).
    *   Middleware de higienização automatizada para mascarar dados sensíveis e credenciais em logs (**R18**).
    *   Versionamento histórico automático de alterações em cursos (**R13**).
    *   Rotina operacional de validação manual de documentos e homologação de instrutores pela equipe de moderação (**R02**).
*   **Justificativa Técnica:** Os logs de auditoria e conformidade devem capturar as interações com todos os controles desenvolvidos nas fases anteriores. Além disso, a homologação de instrutores é um fluxo que se beneficia da plataforma já estar totalmente testada e funcional.

---

## 2.12 Estimativa do Risco Residual
A tabela a seguir apresenta a projeção de mitigação esperada para os 24 riscos lógicos mapeados na plataforma Nexora após a implementação completa e o teste funcional de todas as contramedidas propostas no Plano de Tratamento (Seção 2.10).

| Risco | Nível Inicial | Nível Residual Esperado | Condição para Aceitar o Residual |
| :--- | :--- | :--- | :--- |
| **R01** | Alto (9) | **Baixo (3)** <br>*(P: 1, I: 3)* | MFA configurado e ativo de forma obrigatória para perfis administrativos e de instrutores, com rate limiting ativado em produção bloqueando requisições excessivas (HTTP 429) por IP. |
| **R02** | Médio (6) | **Baixo (2)** <br>*(P: 1, I: 2)* | Fluxo operacional de validação humana e homologação de documentos documentado e executado sem exceções para todo novo perfil de instrutor no banco. |
| **R03** | Alto (9) | **Baixo (3)** <br>*(P: 1, I: 3)* | Diretivas SPF, DKIM e DMARC (`p=reject`) ativas no DNS público e relatórios periódicos de conformidade de e-mail sem ocorrência de spoofing do domínio institucional. |
| **R04** | Médio (6) | **Baixo (2)** <br>*(P: 1, I: 2)* | Endpoint de callback validando assinaturas HMAC de forma estrita e consulta reversa automatizada via API do gateway operando sem falhas de timeout. |
| **R05** | Alto (9) | **Baixo (3)** <br>*(P: 1, I: 3)* | Middleware de validação síncrona de tempo assistido no servidor ativo e testes automatizados de emissão de certificado rejeitando progresso inválido de forma síncrona. |
| **R06** | Alto (9) | **Baixo (3)** <br>*(P: 1, I: 3)* | Bloco de código de cálculo financeiro movido integralmente para o servidor, com front-end atuando apenas como interface visual e back-end revalidando preços de cupons. |
| **R07** | Médio (6) | **Baixo (2)** <br>*(P: 1, I: 2)* | Regras de unicidade de gabarito e controle de propriedade de notas de avaliações ativos no banco de dados e verificados por testes de unidade. |
| **R08** | Alto (9) | **Baixo (3)** <br>*(P: 1, I: 3)* | Emissão automática de código hash baseado nos metadados do certificado gravada no banco e endpoint `/validar-certificado` testado e aberto para consulta pública. |
| **R09** | Médio (6) | **Baixo (2)** <br>*(P: 1, I: 2)* | Validador de Magic Bytes de arquivos ativo e integração com biblioteca ClamAV realizando varreduras síncronas em uploads temporários antes do armazenamento definitivo. |
| **R10** | Médio (6) | **Baixo (2)** <br>*(P: 1, I: 2)* | Middleware de autorização validando matrícula ativa e progresso mínimo antes de aceitar requisições de avaliação de cursos. |
| **R11** | Baixo (2) | **Baixo (1)** <br>*(P: 1, I: 1)* | Processamento de gamificação e regras de conquistas executados como jobs assíncronos no servidor de banco de dados, sem endpoints de escrita expostos ao cliente. |
| **R12** | Alto (9) | **Médio (6)** <br>*(P: 2, I: 3)* | Gravação consistente de logs transacionais contendo geolocalização por IP, fingerprint e timestamp de aceite de termos. O risco residual médio é aceito por limitações de disputas comerciais de má-fé (*chargeback*). |
| **R13** | Médio (4) | **Baixo (2)** <br>*(P: 1, I: 2)* | Tabela de histórico e versionamento de conteúdo ativa, com triggers do banco de dados impedindo a deleção física de aulas sem registro de auditoria. |
| **R14** | Alto (8) | **Baixo (2)** <br>*(P: 1, I: 2)* | Daemon de replicação de logs administrativos em tempo real rodando ativamente e servidor syslog isolado configurado com permissão exclusiva de leitura. |
| **R15** | Alto (9) | **Baixo (3)** <br>*(P: 1, I: 3)* | Middleware centralizado de autorização ao nível do objeto (BOLA/IDOR) implementado, com testes automatizados cobrindo tentativas de acesso cruzado de dados de perfis. |
| **R16** | Alto (9) | **Médio (6)** <br>*(P: 2, I: 3)* | URLs assinadas temporárias geradas pelo S3 ativas e streaming HLS configurado. O risco residual é aceito devido à possibilidade técnica de gravação de tela analógica por software local do aluno. |
| **R17** | Médio (4) | **Baixo (2)** <br>*(P: 2, I: 1)* | Padronização de mensagens de erro de autenticação e equalização do tempo de resposta das requisições implementadas em produção. |
| **R18** | Médio (6) | **Baixo (2)** <br>*(P: 1, I: 2)* | Middleware de sanitização ativado no pipeline de logs do servidor, mascarando chaves sensíveis como senhas e chaves bancárias por tags `[REDACTED]`. |
| **R19** | Alto (9) | **Baixo (3)** <br>*(P: 1, I: 3)* | Regras de auto-scaling de contêineres testadas sob carga extrema e serviços de distribuição estática de mídias por meio de CDN global ativos em produção. |
| **R20** | Médio (6) | **Baixo (2)** <br>*(P: 1, I: 2)* | Middleware de limitação física de upload (`HTTP 413`) testado e quotas rígidas de armazenamento por conta de instrutor ativas no banco de dados. |
| **R21** | Médio (6) | **Baixo (2)** <br>*(P: 1, I: 2)* | Mecanismo de reCAPTCHA ativado dinamicamente após 3 falhas seguidas no login e rate limiting agindo por IP em vez de bloquear o usuário. |
| **R22** | Alto (8) | **Baixo (2)** <br>*(P: 1, I: 2)* | Middleware de validação de JWT configurado para recusar chaves com algoritmo "none" e assinaturas de segredos fracos, validado com suíte de testes automáticos. |
| **R23** | Crítico (12) | **Baixo (3)** <br>*(P: 1, I: 3)* | Injeção obrigatória do middleware global de controle de acesso baseado em papéis (*RBAC*) em todas as rotas administrativas e endpoints privados do painel. |
| **R24** | Alto (8) | **Baixo (2)** <br>*(P: 1, I: 2)* | Integração do plugin de varredura de dependências (SCA) no pipeline CI/CD, bloqueando deploys automáticos caso surjam falhas críticas sem mitigação documentada. |

---

## 2.13 Considerações Finais da Etapa 2
A realização desta etapa demonstrou como traduzir os cenários teóricos identificados na modelagem de ameaças para planos práticos de mitigação baseados em custos, responsabilidades e viabilidade operacional. O uso das funções do NIST CSF 2.0 serviu como norteador de resultados, assegurando que o gerenciamento de incidentes na Nexora não se limite apenas à prevenção técnica, mas também contemple a governança e processos de negócio.

A lição mais relevante para o projeto reside no fato de que os controles de segurança precisam coexistir com a usabilidade e a viabilidade financeira da startup. Reduzir e tratar o risco residual, em vez de focar em uma busca irreal por risco zero, permite que o time dedique recursos técnicos aos pontos que efetivamente trariam consequências graves para o faturamento e para os dados dos usuários protegidos por lei.

Os controles estabelecidos nesta fase servirão de base para a Etapa 3, na qual os requisitos de segurança derivados guiarão a consolidação e o desenho da arquitetura segura e dos limites de confiança do software Nexora.
