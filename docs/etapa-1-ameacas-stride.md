# 1 — Casos de Abuso e Modelagem de Ameaças com STRIDE

## 1.1 Identificação do Sistema

* **Nome do Sistema:** Nexora (Plataforma de Ensino e Aprendizagem Online)
* **Empresa/Cliente:** Nexora Educação Digital LTDA.
* **Integrantes do Grupo:**
  * **Bruna Rosa Ferreira** — brunarf.aluno@unipampa.edu.br — [GitHub](https://github.com/eumuno)
  * **Erik Bruckmann Soares** — eriksoares.aluno@unipampa.edu.br — [GitHub](https://github.com/Erikbruckmann1)
  * **Gabriela Muniz Barreto** — gabrielamuniz.aluno@unipampa.edu.br — [GitHub](https://github.com/gabrielamnzb)
  * **Inaurrara Flores Rozado** — inaurrararozado.aluno@unipampa.edu.br — [GitHub](https://github.com/Inaurrara/)
* **Endereço do Repositório:** `https://github.com/eumuno/ess-nexora`
* **Justificativa da Escolha:** 
  A escolha da plataforma Nexora fundamenta-se na relevância de sistemas de e-learning no cenário educacional atual e no alto volume de dados sensíveis transacionados. A aplicação lida com múltiplos perfis de acesso, processamento de pagamentos, proteção de propriedade intelectual (vídeos e materiais educativos) e dados pessoais protegidos por legislações como a LGPD. Além disso, incidentes recentes no mercado envolvendo vazamentos de dados em plataformas EAD evidenciam a necessidade de incorporar a segurança desde a fase de concepção e modelagem de requisitos.
  
---

## 1.2 Descrição do Sistema

### Visão Geral e Problema Resolvido
A Nexora é uma startup voltada para a educação digital que conecta alunos e instrutores independentes em um ambiente web e mobile. O sistema resolve o problema de distribuição, comercialização e consumo de cursos profissionalizantes e de tecnologia, permitindo a gestão completa do aprendizado em uma única plataforma.

### Público-Alvo e Perfis de Usuários
* **Visitantes:** Usuários não autenticados que navegam pelo catálogo e visualizam prévias dos cursos.
* **Alunos:** Usuários que adquirem cursos, assistem videoaulas, realizam avaliações, participam de fóruns e baixam certificados.
* **Instrutores:** Profissionais que criam, publicam e gerenciam conteúdos de cursos, além de acompanhar o desempenho dos alunos.
* **Administradores / Moderadores:** Atores internos responsáveis pela gestão de usuários, moderação de conteúdos, auditoria de logs e suporte financeiro.

### Principais Funcionalidades
1. Cadastro, autenticação e recuperação de credenciais de acesso.
2. Catálogo de cursos com busca, filtros e recomendações personalizadas.
3. Processamento de pagamentos para compra de cursos e assinaturas.
4. Player de vídeo para transmissão de aulas ao vivo e gravadas.
5. Emissão e validação de certificados digitais de conclusão.
6. Fórum de dúvidas e interação entre alunos e instrutores.
7. Painel administrativo para gestão do sistema e visualização de relatórios.

### Informações Armazenadas e Recursos a Serem Protegidos
* **Dados Pessoais e Credenciais:** Nome, CPF, e-mail, senhas (hashes) e dados de perfil de alunos e instrutores.
* **Informações Financeiras:** Histórico de transações, dados de cobrança e integração com gateways de pagamento.
* **Propriedade Intelectual:** Arquivos de vídeo, PDFs, avaliações e código-fonte dos cursos.
* **Ativos Críticos de Infraestrutura:** Banco de dados relacional, servidores de streaming de vídeo, APIs de integração e logs de auditoria.

---

## 1.3 Usuários, Ativos e Pontos de Interação

### 1.3.1 Usuários e perfis de acesso

| Usuário ou perfil | Principais ações | Nível de acesso |
| --- | --- | --- |
| Visitante | Consultar o catálogo, pesquisar cursos e visualizar conteúdos de demonstração | Público, sem autenticação |
| Aluno | Comprar e acessar cursos, assistir a aulas, baixar materiais, realizar avaliações, participar de fóruns, acompanhar trilhas e emitir certificados | Autenticado, restrito aos próprios dados e aos cursos adquiridos |
| Instrutor | Criar e atualizar cursos, enviar vídeos e materiais, elaborar avaliações, corrigir atividades, interagir nos fóruns e consultar o desempenho dos alunos | Autenticado, restrito aos cursos sob sua responsabilidade |
| Moderador | Analisar denúncias, moderar fóruns e conteúdos e aplicar medidas previstas pelas políticas da plataforma | Acesso interno privilegiado e limitado às funções de moderação |
| Administrador | Gerenciar contas, perfis e permissões, remover conteúdos, consultar logs e configurar recursos da plataforma | Acesso interno privilegiado a funções administrativas |
| Equipe financeira | Consultar transações, assinaturas, repasses, estornos e relatórios financeiros | Acesso interno restrito às operações financeiras |

Todos os perfis autenticados utilizam credenciais individuais. As permissões devem seguir o princípio do menor privilégio, especialmente para moderadores, administradores e membros da equipe financeira.

### 1.3.2 Ativos importantes

Ativos são dados ou recursos cujo acesso, alteração, destruição ou indisponibilidade indevidos podem causar prejuízo à Nexora e aos seus usuários.

| Ativo | Exemplos | Requisito de segurança predominante | Possível prejuízo em caso de comprometimento |
| --- | --- | --- | --- |
| Credenciais e sessões | Hashes de senhas, tokens de acesso, códigos de recuperação e sessões autenticadas | Confidencialidade e integridade | Invasão de contas e execução de ações em nome da vítima |
| Dados pessoais | Nome, CPF, e-mail, dados de perfil e informações de instrutores | Confidencialidade e integridade | Violação de privacidade, fraude e descumprimento da LGPD |
| Dados financeiros | Histórico de compras, assinaturas, cobranças, repasses e identificadores de transação | Confidencialidade e integridade | Fraude, prejuízo financeiro e contestação de pagamentos |
| Conteúdo educacional | Videoaulas, transmissões ao vivo, PDFs, avaliações e materiais complementares | Confidencialidade, integridade e disponibilidade | Pirataria, alteração ou perda de propriedade intelectual |
| Registros acadêmicos | Matrículas, progresso, notas, respostas de avaliações, trilhas e certificados | Integridade e disponibilidade | Certificados inválidos, perda do progresso e resultados incorretos |
| Fóruns e avaliações | Mensagens, denúncias, comentários e avaliações dos cursos | Integridade e disponibilidade | Assédio, desinformação, fraude de reputação e perda de confiança |
| Perfis e permissões | Papéis de aluno, instrutor, moderador, administrador e financeiro | Integridade | Elevação indevida de privilégios e acesso a funções restritas |
| Logs de auditoria | Registros de login, operações administrativas, pagamentos e alterações relevantes | Integridade e disponibilidade | Perda de rastreabilidade e dificuldade de investigar incidentes |
| Disponibilidade da plataforma | Portal, aplicativo, API, autenticação, streaming e aulas ao vivo | Disponibilidade | Interrupção das aulas, perda de vendas e dano à reputação |
| Algoritmos e dados de recomendação | Preferências, histórico de consumo, trilhas e resultados de gamificação | Confidencialidade e integridade | Recomendações manipuladas, exposição de hábitos e perda de confiança |

Os ativos mais críticos são as credenciais e sessões, os dados pessoais e financeiros, as permissões administrativas, os registros acadêmicos, os conteúdos dos cursos e os logs de auditoria.

### 1.3.3 Pontos de interação e componentes

| Elemento | Função e dados envolvidos |
| --- | --- |
| Portal web e aplicativo móvel | Interfaces utilizadas para cadastro, autenticação, compra, consumo e administração de cursos |
| Serviço de autenticação e autorização | Valida credenciais, emite sessões ou tokens e verifica as permissões de cada perfil |
| API da Nexora | Recebe as requisições das interfaces e aplica as regras de negócio e de autorização |
| Banco de dados | Armazena contas, perfis, cursos, matrículas, progresso, avaliações, fóruns, certificados e referências de transações |
| Armazenamento de conteúdo e serviço de streaming | Guarda e entrega vídeos, aulas ao vivo, imagens, PDFs e outros materiais educacionais |
| Gateway de pagamento | Processa compras, assinaturas, estornos e confirmações financeiras; a Nexora deve evitar armazenar dados completos de cartão |
| Serviço de notificações | Envia mensagens de confirmação, recuperação de conta, avisos de aula e alertas de segurança por e-mail ou push |
| Mecanismo de recomendações e gamificação | Processa preferências, histórico de aprendizagem, pontuações, conquistas e sugestões de cursos |
| Painel administrativo | Permite moderação, suporte, gestão de contas e permissões, consulta de logs e acompanhamento financeiro |
| Sistema de logs e monitoramento | Registra eventos relevantes, detecta comportamentos suspeitos e apoia auditorias e resposta a incidentes |

Os principais pontos de entrada sujeitos a abuso são as telas e APIs de autenticação e recuperação de senha, upload e download de arquivos, fóruns, avaliações, pagamentos, emissão e validação de certificados e funções administrativas.

---

## 1.4 Visão Geral da Arquitetura ou Fluxo

A Nexora utiliza interfaces web e mobile para conectar visitantes, alunos, instrutores e usuários internos aos serviços da plataforma. As requisições autenticadas passam pela API, que consulta o serviço de autenticação e autorização antes de acessar dados ou executar operações. Vídeos e materiais são armazenados separadamente do banco de dados, enquanto pagamentos e notificações dependem de serviços externos.

![Diagrama de contexto e fluxo de dados da Nexora](../diagramas/etapa-1/diagrama-contexto-nexora.png)

Arquivo-fonte editável: [diagrama-contexto-nexora.drawio](../diagramas/etapa-1/diagrama-contexto-nexora.drawio).

### 1.4.1 Fluxos principais de dados

| ID | Origem e destino | Dados ou operação | Proteção necessária |
| --- | --- | --- | --- |
| F01 | Usuários → Portal/App → Autenticação | Credenciais, recuperação de conta e tokens de sessão | TLS, hash seguro de senhas, limitação de tentativas e autenticação reforçada para acessos privilegiados |
| F02 | Portal/App → API → Banco de dados | Perfis, cursos, matrículas, progresso, avaliações, fóruns e certificados | Autorização no servidor, validação de entrada, criptografia e registro de operações relevantes |
| F03 | Instrutor → API → Armazenamento/Streaming | Upload de vídeos, PDFs, imagens e aulas ao vivo | Validação de tipo e tamanho, análise de arquivos, autorização e proteção contra acesso não contratado |
| F04 | Aluno → API → Gateway de pagamento | Pedido, valor, assinatura e identificador da transação | TLS, validação do valor no servidor, tokenização e verificação da resposta do gateway |
| F05 | API → Serviço de notificações → Usuário | Confirmações, avisos, recuperação de conta e alertas | Evitar dados sensíveis desnecessários, autenticar a integração e proteger links e códigos temporários |
| F06 | API → Recomendações/Gamificação → Banco de dados | Histórico de aprendizagem, preferências, pontos e conquistas | Minimização de dados, controle de acesso e prevenção de manipulação de pontuações |
| F07 | Painel administrativo → API → Logs/Banco de dados | Gestão de usuários e permissões, moderação, suporte e auditoria | Menor privilégio, autenticação forte, trilha de auditoria e revisão de acessos |

### 1.4.2 Limites de confiança

O primeiro limite de confiança está entre os dispositivos dos usuários e a infraestrutura da Nexora, pois todo dado recebido do navegador ou aplicativo deve ser tratado como não confiável. O segundo está entre a API e os serviços externos de pagamento e notificações, cujas respostas precisam ser autenticadas e validadas. O terceiro separa as funções comuns das funções privilegiadas do painel administrativo. O quarto protege a camada de dados, na qual somente serviços autorizados devem acessar o banco, o armazenamento de conteúdo e os logs.

---

## 1.5 Modelagem de Ameaças com STRIDE
A modelagem a seguir aplica o método STRIDE aos componentes, ativos e fluxos
identificados nas Seções 1.3 e 1.4. A análise adota a perspectiva de um agente
malicioso externo ou interno sobre os pontos de entrada mais expostos da
Nexora: autenticação e recuperação de senha, upload e consumo de conteúdo,
avaliações, emissão de certificados, pagamentos e funções administrativas. A
coluna *Fluxo* referencia os fluxos de dados descritos na Seção 1.4.1.

| ID | Categoria STRIDE | Componente ou ativo | Fluxo | Ameaça identificada | Possível impacto |
| --- | --- | --- | --- | --- | --- |
| T01 | Spoofing | Serviço de autenticação e autorização / Credenciais e sessões | F01 | Atacante reutiliza pares de e-mail e senha vazados de outros serviços para acessar contas da Nexora | Invasão de contas, acesso a dados pessoais e uso indevido de cursos adquiridos |
| T02 | Spoofing | Portal web e aplicativo móvel / Perfis e permissões | F01 | Pessoa mal-intencionada cadastra-se como instrutor usando o nome de um profissional ou marca conhecida, sem verificação de identidade | Alunos enganados, prejuízo financeiro e dano à reputação da plataforma |
| T03 | Spoofing | Serviço de notificações | F05 | Mensagens de recuperação de conta são imitadas por terceiros, pois o remetente não é autenticado nem os links protegidos | Captura de credenciais por phishing e comprometimento de contas legítimas |
| T04 | Spoofing | Gateway de pagamento / API da Nexora | F04 | Requisição forjada ao endpoint de retorno do gateway simula a confirmação de um pagamento que não ocorreu | Liberação de cursos e assinaturas sem receita correspondente |
| T05 | Tampering | API da Nexora / Registros acadêmicos | F02 | Aluno intercepta e altera a requisição que marca aulas como concluídas, atingindo o percentual necessário sem assistir ao conteúdo | Emissão de certificado indevido e perda de valor dos certificados da Nexora |
| T06 | Tampering | API da Nexora / Dados financeiros | F04 | Valor do curso ou percentual do cupom é alterado no cliente antes do envio, pois o preço não é reconferido no servidor | Prejuízo financeiro direto e inconsistência nos repasses aos instrutores |
| T07 | Tampering | API da Nexora / Registros acadêmicos | F02 | Respostas de avaliação já enviadas ou a nota final são alteradas por meio de chamada direta à API de correção | Fraude acadêmica e perda de confiabilidade das avaliações |
| T08 | Tampering | Registros acadêmicos (certificados) | F02 | Certificado é emitido sem assinatura digital ou código de verificação, permitindo edição do nome, do curso e da carga horária | Falsificação de certificados e responsabilização da plataforma |
| T09 | Tampering | Armazenamento de conteúdo e serviço de streaming / Conteúdo educacional | F03 | Instrutor substitui ou envia arquivos sem validação de tipo e tamanho, incluindo conteúdo malicioso disfarçado de material de apoio | Distribuição de malware aos alunos e comprometimento da integridade do curso |
| T10 | Tampering | Fóruns e avaliações | F02 | Instrutor cria contas de aluno para publicar avaliações positivas no próprio curso e negativas em concorrentes | Fraude de reputação, distorção do catálogo e perda de confiança dos usuários |
| T11 | Tampering | Mecanismo de recomendações e gamificação | F06 | Pontuações, conquistas e histórico de aprendizagem são manipulados por requisições diretas à API | Rankings e recomendações distorcidos e vantagem indevida em trilhas gamificadas |
| T12 | Repudiation | Logs de auditoria / Dados financeiros | F04 | Usuário nega ter realizado uma compra e solicita estorno; o sistema não registra data, endereço de origem e meio de pagamento da transação | Perda financeira por contestações indevidas e impossibilidade de defesa |
| T13 | Repudiation | Logs de auditoria / Conteúdo educacional | F03 | Instrutor remove ou altera aulas de um curso já vendido e nega a alteração, por ausência de trilha de auditoria e versionamento | Conflito com alunos e mediação sem evidências |
| T14 | Repudiation | Painel administrativo / Logs de auditoria | F07 | Ações privilegiadas como exclusão de contas, estornos e remoção de conteúdo não são registradas de forma íntegra e atribuível | Perda de rastreabilidade, abuso interno impune e dificuldade de apuração |
| T15 | Information Disclosure | API da Nexora / Dados pessoais | F02 | Falha de autorização em endpoint por identificador permite que um usuário autenticado consulte dados de qualquer outro | Exposição de dados pessoais em larga escala e descumprimento da LGPD |
| T16 | Information Disclosure | Armazenamento de conteúdo e serviço de streaming / Conteúdo educacional | F03 | Vídeos e materiais ficam acessíveis por URL pública e permanente, sem verificação de matrícula, e podem ser compartilhados fora da plataforma | Pirataria do conteúdo pago e prejuízo a instrutores e à Nexora |
| T17 | Information Disclosure | Serviço de autenticação e autorização | F01 | Mensagens de erro distintas para e-mail inexistente e senha incorreta permitem enumerar usuários cadastrados | Construção de listas de alvos para phishing e ataques de força bruta |
| T18 | Information Disclosure | Sistema de logs e monitoramento | F07 | Dados pessoais, tokens de sessão e códigos de recuperação são gravados em texto claro nos registros de aplicação | Vazamento de credenciais e dados sensíveis a quem tenha acesso aos logs |
| T19 | Denial of Service | Disponibilidade da plataforma (streaming e aulas ao vivo) | F03 | Volume massivo de requisições concentrado no horário de uma aula ao vivo ou de um prazo de avaliação | Indisponibilidade em momento crítico, evasão de alunos e pedidos de reembolso |
| T20 | Denial of Service | Armazenamento de conteúdo e serviço de streaming | F03 | Envio repetido de arquivos muito grandes, sem limite de tamanho ou de taxa, esgotando armazenamento e banda | Degradação do serviço para todos os usuários e aumento do custo de infraestrutura |
| T21 | Denial of Service | Serviço de autenticação e autorização / Credenciais e sessões | F01 | Tentativas propositais de login incorreto contra contas conhecidas disparam o bloqueio automático das vítimas | Alunos e instrutores legítimos impedidos de acessar a plataforma |
| T15 | Information Disclosure | API da Nexora / Dados pessoais | F02 | Falha de autorização em endpoint por identificador permite que um usuário autenticado consulte dados de qualquer outro | Exposição de dados pessoais em larga escala e descumprimento da LGPD |
| T16 | Information Disclosure | Armazenamento de conteúdo e serviço de streaming / Conteúdo educacional | F03 | Vídeos e materiais ficam acessíveis por URL pública e permanente, sem verificação de matrícula, e podem ser compartilhados fora da plataforma | Pirataria do conteúdo pago e prejuízo a instrutores e à Nexora |
| T17 | Information Disclosure | Serviço de autenticação e autorização | F01 | Mensagens de erro distintas para e-mail inexistente e senha incorreta permitem enumerar usuários cadastrados | Construção de listas de alvos para phishing e ataques de força bruta |
| T18 | Information Disclosure | Sistema de logs e monitoramento | F07 | Dados pessoais, tokens de sessão e códigos de recuperação são gravados em texto claro nos registros de aplicação | Vazamento de credenciais e dados sensíveis a quem tenha acesso aos logs |
| T19 | Denial of Service | Disponibilidade da plataforma (streaming e aulas ao vivo) | F03 | Volume massivo de requisições concentrado no horário de uma aula ao vivo ou de um prazo de avaliação | Indisponibilidade em momento crítico, evasão de alunos e pedidos de reembolso |
| T20 | Denial of Service | Armazenamento de conteúdo e serviço de streaming | F03 | Envio repetido de arquivos muito grandes, sem limite de tamanho ou de taxa, esgotando armazenamento e banda | Degradação do serviço para todos os usuários e aumento do custo de infraestrutura |
| T21 | Denial of Service | Serviço de autenticação e autorização / Credenciais e sessões | F01 | Tentativas propositais de login incorreto contra contas conhecidas disparam o bloqueio automático das vítimas | Alunos e instrutores legítimos impedidos de acessar a plataforma |
| T22 | Elevation of Privilege | Serviço de autenticação e autorização / Perfis e permissões | F01, F02 | O papel do usuário é aceito a partir do cliente ou de um token cuja integridade não é validada no servidor | Aluno passa a publicar cursos ou a acessar funções internas e financeiras |
| T23 | Elevation of Privilege | Painel administrativo | F07 | Rotas administrativas ficam ocultas apenas na interface, sem verificação de permissão na API, acessíveis a qualquer usuário autenticado | Acesso a dados financeiros, exclusão de cursos e alteração de contas |
| T24 | Elevation of Privilege | API da Nexora / Banco de dados | F02 | Exploração de vulnerabilidade conhecida em biblioteca desatualizada utilizada pelo servidor de aplicação | Comprometimento do servidor e acesso irrestrito à camada de dados |
---

## 1.6 Casos de Abuso (Abuse Cases)

##### CA01 — Download e Distribuição Não Autorizada de Videoaulas (Pirataria)
* **Ator Malicioso:** Aluno mal-intencionado.
* **Objetivo:** Baixar as videoaulas do portal para distribuí-las ou comercializá-las ilegalmente fora do sistema.
* **Ativos Afetados (Seção 1.3.2):** Conteúdo educacional.
* **Fluxos de Dados Envolvidos (Seção 1.4.1):** **F02** (Portal/App → API → Banco de dados para acessar o curso) e **F03** (Instrutor → API → Armazenamento/Streaming para reprodução do vídeo).
* **Condições Necessárias:**
  * O aluno possui uma conta ativa no curso.
  * O player de vídeo não possui mecanismos que impeçam o download direto do arquivo de mídia pelo navegador.
* **Fluxo de Abuso:**
  1. O aluno realiza o login legítimo na plataforma.
  2. Navega até a página de reprodução de uma das videoaulas do curso.
  3. Abre as ferramentas do desenvolvedor do próprio navegador para capturar o link direto de origem do arquivo de vídeo.
  4. Realiza o download direto do arquivo de vídeo.
  5. Salva o arquivo localmente e o compartilha em sites externos de pirataria.
* **Impacto Esperado:** Perda de propriedade intelectual dos instrutores, prejuízo financeiro para a plataforma e desvalorização do catálogo de cursos.
* **Categorias STRIDE Relacionadas:** *Information Disclosure* (Vazamento de Informação).

---

##### CA02 — Geração Fraudulenta de Certificado de Conclusão de Curso
* **Ator Malicioso:** Aluno mal-intencionado.
* **Objetivo:** Obter o certificado digital de conclusão sem assistir às aulas ou realizar as avaliações exigidas.
* **Ativos Afetados (Seção 1.3.2):** Registros acadêmicos, Perfis e permissões.
* **Fluxos de Dados Envolvidos (Seção 1.4.1):** **F02** (Portal/App → API → Banco de dados para processamento e emissão de registros).
* **Condições Necessárias:**
  * O sistema de geração de certificados confia apenas na interface visual do navegador (front-end) para liberar o documento, sem que o servidor de banco de dados valide novamente o progresso real e as notas do estudante.
* **Fluxo de Abuso:**
  1. O aluno acessa a página do curso em que está matriculado.
  2. Ele simula/força a requisição de emissão do certificado diretamente para o sistema, ignorando o fluxo comum de visualização das aulas.
  3. O servidor processa a solicitação sem revalidar as regras de conclusão no banco de dados.
  4. O sistema gera o certificado em PDF assinado pela Nexora.
  5. O aluno faz o download do documento inválido.
* **Impacto Esperado:** Geração de certificados inválidos, fraude acadêmica e perda de credibilidade do processo de ensino da plataforma Nexora.
* **Categorias STRIDE Relacionadas:** *Tampering* (Adulteração de dados do fluxo lógico) e *Elevation of Privilege* (Obtenção de uma validação/privilégio indevido).

---

##### CA03 — Sequestro de Conta de Instrutor para Desvio de Repasses Financeiros
* **Ator Malicioso:** Atacante externo.
* **Objetivo:** Alterar a chave de recebimento Pix ou os dados bancários de um instrutor para desviar os valores das vendas de cursos.
* **Ativos Afetados (Seção 1.3.2):** Credenciais e sessões, Dados pessoais, Dados financeiros.
* **Fluxos de Dados Envolvidos (Seção 1.4.1):** **F01** (Autenticação) e **F07** (Painel administrativo → API → Logs/Banco de dados para alteração dos dados).
* **Ameaças STRIDE Relacionadas (Seção 1.5):** Diretamente associado à ameaça **T01** (Uso de credenciais roubadas).
* **Condições Necessárias:**
  * O atacante consegue a senha do instrutor (por meio de senhas fracas ou vazamento de credenciais em outros sites).
  * A plataforma não exige uma confirmação adicional (como digitar a senha novamente ou enviar um código por e-mail) antes de salvar alterações nos dados de faturamento.
* **Fluxo de Abuso:**
  1. O atacante faz o login no sistema utilizando as credenciais obtidas do instrutor legítimo.
  2. Navega até a página de perfil financeiro no painel do instrutor.
  3. Substitui os dados bancários ou chave Pix do instrutor pelos seus próprios dados.
  4. O sistema salva a alteração de faturamento sem exigir nenhuma autenticação extra.
  5. No período de repasse das comissões, os valores das vendas dos cursos são depositados na conta do atacante.
* **Impacto Esperado:** Desvio de repasses, prejuízo financeiro ao instrutor e danos graves à reputação e à confiabilidade da marca Nexora.
* **Categorias STRIDE Relacionadas:** *Spoofing* (Falsificação de Identidade para acesso à conta) e *Tampering* (Adulteração de Dados Financeiros).

---

##### CA04 — Visualização Insegura de Perfis de Outros Alunos (Manipulação de Parâmetros)
* **Ator Malicioso:** Aluno mal-intencionado.
* **Objetivo:** Acessar informações pessoais e privadas de outros alunos cadastrados no sistema.
* **Ativos Afetados (Seção 1.3.2):** Dados pessoais.
* **Fluxos de Dados Envolvidos (Seção 1.4.1):** **F02** (Portal/App → API → Banco de dados para consultar e exibir o perfil de membros no fórum).
* **Condições Necessárias:**
  * O sistema exibe o perfil do usuário na tela usando um identificador numérico visível (ex: `id=123`), mas não valida no servidor se o usuário logado tem permissão para requisitar os dados privados de outros cadastros.
* **Fluxo de Abuso:**
  1. O aluno faz o login no portal com seu usuário legítimo.
  2. Acessa a sua própria página de perfil e observa o identificador numérico na barra de endereços do navegador (ex: `https://nexora.com/perfil?id=1050`).
  3. Altera manualmente o número do identificador na barra de endereços para outro valor (ex: `https://nexora.com/perfil?id=1051`).
  4. O sistema carrega e exibe na tela os dados privados (como e-mail e CPF) correspondentes ao outro aluno, sem realizar o bloqueio.
* **Impacto Esperado:** Exposição indevida de dados pessoais, violação de privacidade dos estudantes e descumprimento das diretrizes da LGPD.
* **Categorias STRIDE Relacionadas:** *Information Disclosure* (Exposição / Divulgação indevida de informações).

---

## 1.7 Considerações Finais
A análise de segurança realizada na plataforma Nexora permitiu mapear riscos significativos que vão além das funcionalidades tradicionais de um sistema de ensino. Ao adotar a perspectiva de um atacante, o grupo identificou vulnerabilidades que poderiam comprometer a reputação da startup e a integridade de seus serviços.

* **Ativos Mais Críticos:** Os recursos que demandam maior proteção são as credenciais e sessões dos usuários (visto que dão acesso total às contas), os dados pessoais e financeiros (com impactos diretos sob a ótica da LGPD e fraudes de repasses), e o conteúdo educacional pago (videoaulas e mídias), que representa a propriedade intelectual central dos instrutores e da empresa.
* **Ameaças Mais Preocupantes:** As categorias do STRIDE que apresentam maior risco operacional e reputacional são a falsificação de identidade (*Spoofing*) para invasão de contas de instrutores e alunos, a adulteração de dados (*Tampering*) direcionada à liberação de certificados de forma indevida ou modificação de chaves de faturamento, e o vazamento de informações confidenciais (*Information Disclosure*) envolvendo dados cadastrais.
* **Casos de Abuso de Maior Impacto:** Entre os cenários modelados, o sequestro de contas de instrutores para o desvio de repasses financeiros (CA03) e a raspagem em massa de dados de alunos no fórum de dúvidas através de falhas de autorização (CA04) foram identificados como os de consequências mais graves, devido aos impactos financeiros diretos, riscos jurídicos perante a legislação de proteção de dados e quebra de confiança do ecossistema.
* **Dificuldades Enfrentadas:** A principal dificuldade residiu na mudança de mentalidade, migrando do desenvolvimento funcional clássico para a simulação sistemática de abusos deliberados por parte de agentes maliciosos. Além disso, compreender as fronteiras de confiança entre a API interna, o banco de dados e os gateways de pagamento externos exigiu um esforço de reflexão para não generalizar as ameaças, atendo-as rigorosamente ao domínio da plataforma Nexora.
