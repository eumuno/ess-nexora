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

![Diagrama de contexto e fluxo de dados da Nexora](diagramas/diagrama-contexto-nexora.png)

Arquivo-fonte editável: [diagrama-contexto-nexora.drawio](diagramas/diagrama-contexto-nexora.drawio).

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
---

## 1.6 Casos de Abuso (Abuse Cases)
##### CA01 — Download e Distribuição Não Autorizada de Videoaulas (Pirataria)
* **Ator Malicioso:** Aluno mal-intencionado ou atacante externo autenticado.
* **Objetivo:** Contornar os controles de reprodução do player de vídeo para baixar e distribuir os arquivos originais de videoaulas protegidas fora da plataforma Nexora.
* **Condições Necessárias:**
  * O atacante possui uma conta de aluno ativa com acesso a pelo menos um curso pago.
  * O player de vídeo ou o servidor de streaming não utiliza mecanismos robustos de criptografia/DRM ou URLs assinadas de curta duração com controle de sessão.
* **Fluxo de Abuso:**
  1. O atacante realiza o login na plataforma Nexora como aluno legítimo.
  2. O atacante acessa a página do curso e inicia a reprodução de uma das videoaulas.
  3. O atacante inspeciona o tráfego de rede pelo navegador (Developer Tools) para identificar a URL direta do servidor de streaming de vídeo ou o arquivo de manifesto de transmissão (.m3u8).
  4. O atacante utiliza um script ou ferramenta de automação para extrair e fazer o download em massa do fluxo de vídeo original diretamente do servidor.
  5. O atacante salva os arquivos localmente e os redistribui ilegalmente em sites de pirataria ou fóruns de terceiros.
* **Impacto Esperado:** Violação de direitos autorais e de propriedade intelectual dos instrutores, prejuízo financeiro direto ao modelo de negócios e desvalorização do catálogo de cursos da plataforma.
* **Categorias STRIDE Relacionadas:** *Information Disclosure* (Exposição não autorizada de propriedade intelectual) e *Tampering* (Burlar/adulterar o fluxo projetado do player).

---

##### CA02 — Geração Fraudulenta de Certificado de Conclusão de Curso
* **Ator Malicioso:** Aluno mal-intencionado.
* **Objetivo:** Obter um certificado digital de conclusão válido, assinado pela Nexora, sem cumprir a carga horária mínima exigida ou realizar as avaliações do curso.
* **Condições Necessárias:**
  * O servidor aceita requisições de geração de certificado por API sem verificar novamente o progresso real e as notas do estudante no banco de dados, confiando apenas nas validações de interface do lado do cliente (front-end).
* **Fluxo de Abuso:**
  1. O aluno mal-intencionado se matricula em um curso na plataforma Nexora.
  2. Sem assistir a nenhuma videoaula ou responder questionários, ele inspeciona e intercepta as requisições enviadas ao servidor.
  3. Ele envia uma requisição HTTP direta à API de emissão de certificados (`/api/v1/certificates/generate`) injetando o ID do curso recém-adquirido.
  4. O servidor processa a chamada diretamente e gera o certificado oficial em PDF autenticado sem revalidar a porcentagem real de conclusão do aluno.
  5. O aluno realiza o download do certificado para fins de comprovação acadêmica ou profissional indevida.
* **Impacto Esperado:** Perda de credibilidade dos certificados emitidos pela Nexora, comprometimento da reputação da marca perante o mercado e desvalorização do processo de aprendizado.
* **Categorias STRIDE Relacionadas:** *Tampering* (Adulteração de fluxo lógico do processo acadêmico) e *Elevation of Privilege* (Obtenção de uma validação/privilégio indevido).

---

##### CA03 — Sequestro de Conta de Instrutor para Desvio de Repasses Financeiros
* **Ator Malicioso:** Atacante externo.
* **Objetivo:** Alterar os dados bancários ou chave Pix de recebimento de um instrutor legítimo para desviar os repasses financeiros das vendas de seus cursos.
* **Condições Necessárias:**
  * O atacante obtém as credenciais de acesso de um instrutor por meio de vazamento de senhas ou engenharia social (*phishing*).
  * A Nexora não exige confirmação adicional (como reautenticação, token de segurança por e-mail ou autenticação multifator - MFA) para alterações de dados financeiros sensíveis.
* **Fluxo de Abuso:**
  1. O atacante autentica-se na plataforma Nexora utilizando as credenciais roubadas do instrutor.
  2. O atacante navega até o painel de configurações de faturamento e perfil do instrutor.
  3. Altera a chave Pix cadastrada para o recebimento mensal dos valores das comissões de venda dos cursos.
  4. O sistema aceita a alteração financeira sem emitir alertas imediatos ou exigir validações extras.
  5. Ao fim do período de faturamento, os valores devidos ao instrutor são transferidos para a conta do atacante.
* **Impacto Esperado:** Prejuízo financeiro direto ao instrutor, potencial necessidade de ressarcimento por parte da Nexora, riscos jurídicos e perda de confiança por parte dos criadores de conteúdo na plataforma.
* **Categorias STRIDE Relacionadas:** *Spoofing* (Falsificação de Identidade para acesso à conta) e *Tampering* (Adulteração de Dados Financeiros).

---

##### CA04 — Raspagem Massiva de Dados Pessoais de Alunos no Fórum de Dúvidas (IDOR)
* **Ator Malicioso:** Usuário autenticado mal-intencionado.
* **Objetivo:** Coletar dados pessoais confidenciais (como e-mail, telefone e CPF) de alunos e instrutores registrados na Nexora em larga escala.
* **Condições Necessárias:**
  * Vulnerabilidade de Referência Direta Insegura a Objeto (IDOR) na API responsável pela consulta de perfis de membros no fórum de dúvidas da plataforma.
  * O servidor de API retorna o conjunto de dados confidenciais completo na resposta JSON, dependendo erroneamente apenas de regras de front-end para ocultar esses dados dos usuários.
* **Fluxo de Abuso:**
  1. O atacante se autentica como aluno e acessa um fórum de dúvidas público de qualquer curso.
  2. Ele navega até o perfil de outro aluno participante e inspeciona a requisição HTTP enviada à API (ex.: `/api/users/profile/1042`).
  3. O atacante percebe que o JSON de resposta da API contém dados confidenciais (CPF, telefone e e-mail) que deveriam estar ocultos.
  4. Ele executa um script automatizado que envia requisições consecutivas iterando os identificadores (`1043`, `1044`, etc.).
  5. O script armazena todos os registros sensíveis expostos, construindo um banco de dados completo das vítimas.
* **Impacto Esperado:** Vazamento massivo de dados de privacidade, quebra de conformidade direta com a LGPD (sujeitando a Nexora Educação Digital LTDA a processos e multas administrativas) e danos graves de reputação à empresa.
* **Categorias STRIDE Relacionadas:** *Information Disclosure* (Exposição indevida de dados pessoais e acadêmicos).

---

## 1.7 Considerações Finais
*(PARTE DA GABRIELA; favor, apagar depois de preencher)*
