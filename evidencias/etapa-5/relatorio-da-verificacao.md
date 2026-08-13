# 5 — Relatório de Verificação de Vulnerabilidades

Este documento consolida a verificação da Etapa 5. O escopo desta atividade consiste na realização de testes de segurança dinâmicos (DAST) em uma aplicação controlada, analisando possíveis falhas de segurança por meio de varreduras automatizadas, interpretando seus alertas e propondo correções.

## 5.1 Ambiente e configuração da verificação

### 5.1.1 Justificativa do Ambiente de Laboratório

A plataforma **Nexora** é um projeto de software concebido de maneira **100% fictícia e documental**, o que significa que o grupo não possui uma implementação web executável em ambiente de produção para ser testada diretamente.

Para suprir essa limitação técnica e cumprir os objetivos pedagógicos da disciplina, a equipe estabeleceu um ambiente de laboratório seguro utilizando o **OWASP Juice Shop**. O Juice Shop é uma aplicação web de código aberto e deliberadamente vulnerável, desenvolvida para fins de treinamento e educação em segurança de aplicações web.

Essa abordagem permite que o grupo execute uma sessão de varredura dinâmica real, avalie as saídas de segurança de uma ferramenta de DAST e desenvolva as análises técnicas de risco necessárias para a consolidação deste relatório.

### 5.1.2 Especificação Técnica do Ambiente de Testes

Para garantir a reprodutibilidade dos testes e a validade científica das evidências, o setup do laboratório foi configurado com os seguintes parâmetros técnicos:
| Item | Informação |
| :--- | :--- |
| Sistema Operacional da Máquina de Testes: | Windows 11 |
| Ambiente de Execução de Containers: | Docker Desktop (v4.85.0) |
| Aplicação Alvo (Testada): | OWASP Juice Shop (v20.1.1) |
| Endereço de Acesso Local: | `http://localhost:3000` |
| Porta de Comunicação: | 3000 |
| Ferramenta de Varredura Ativa (DAST): | OWASP ZAP (ZAP Version: 2.17.0) |
| Escopo do Teste: | Restrito estritamente à aplicação Juice Shop rodando localmente na porta de testes da equipe, sem interações com serviços de terceiros externos ou sistemas públicos de produção. |
| Data de Execução da Varredura: | 09 de agosto de 2026 |
| Responsável pela Execução: | Bruna Rosa Ferreira |

---

### 5.1.3 Evidências da Execução (Varredura de Segurança)

Os registros visuais a seguir comprovam a configuração do ambiente de laboratório, a execução da varredura automatizada pelo OWASP ZAP e os alertas identificados durante a sessão de testes. Todos os arquivos estão salvos na pasta de evidências do repositório.

#### 1. Aplicação-Alvo em Execução

O container Docker do OWASP Juice Shop foi iniciado e disponibilizado localmente na porta 3000.
![01 - Juice Shop Executando](01-juice-shop-executando.png)

#### 2. Configuração de Escopo e Alvo no OWASP ZAP

Configuração do OWASP ZAP apontando a varredura para a instância local do Juice Shop.
![02 - ZAP Configuração Básica](02-zap-configuracao-basica.png)

#### 3. Início da Varredura e Mapeamento da Aplicação

Registro do início da sessão de testes realizada pelo OWASP ZAP, incluindo o processo inicial de identificação e mapeamento dos recursos da aplicação.
![03 - ZAP Varredura Iniciando](03-zap-varredura-iniciando.png)

#### 4. Varredura Dinâmica em Progresso (Active Scan)

Registro da execução do Active Scan do OWASP ZAP sobre a aplicação-alvo, durante a qual a ferramenta realizou requisições automatizadas para verificar possíveis vulnerabilidades.
![04 - ZAP Varredura Ativa](04-zap-varredura-ativa.png)

#### 5. Painel Consolidado de Alertas Gerados

Painel de alertas do OWASP ZAP apresentando os achados identificados durante a sessão de verificação e suas respectivas classificações de risco.
![05 - ZAP Alertas Gerados](05-zap-alertas-gerados.png)

#### 6. Efeito Visível das Requisições de Ataque no Alvo

Registro do comportamento observado na interface do OWASP Juice Shop durante a execução das requisições automatizadas realizadas pelo scanner.
![06 - Efeito do Ataque no JuiceShop](06-efeito-do-ataque-no-juiceshop.png)

## 5.2 Registro consolidado dos achados

| ID  | Alerta ou achado                             | Evidência                                                                                                                                                                                                                            | Possível impacto                                                                                                                                                                                                       | Relação CWE/OWASP                                                                                                                                | Correção proposta                                                                                                                                                                                |
| :-: | :------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A01 | **Injeção SQL (SQL Injection)**              | Alerta vermelho (High) no painel de alertas do OWASP ZAP (Active Scan), evidenciando que parâmetros enviados via requisições HTTP foram concatenados de forma direta em queries executadas no banco de dados da aplicação de testes. | Um atacante pode burlar a autenticação (ex.: logar como administrador sem senha), extrair dados sensíveis (LGPD), alterar notas/certificados de alunos ou comprometer totalmente o servidor do banco de dados.         | CWE-89 (Neutralização Inadequada de Elementos Especiais em Comandos SQL) e OWASP Top 10:2021 — A03: Injection.                                   | Implementar o uso mandatório de consultas parametrizadas (Prepared Statements) ou a utilização segura de um mapeador objeto-relacional (ORM), além de sanitização rigorosa de inputs no backend. |
| A02 | **Redirecionamento Externo (Open Redirect)** | Alerta vermelho (High) no painel do OWASP ZAP, com exploração confirmada pela resolução do desafio _Allowlist Bypass_ registrada na captura `06-efeito-do-ataque-no-juiceshop.png`.                                                  | Um atacante pode construir um endereço que começa no domínio legítimo e termina em um site sob seu controle, aumentando a eficácia de campanhas de phishing e podendo vazar dados de sessão pelo cabeçalho `Referer`.  | CWE-601 (Redirecionamento de URL para Site Não Confiável) e OWASP Top 10:2021 — A01: Broken Access Control.                                      | Substituir o destino recebido do cliente por identificadores internos resolvidos no servidor e validar o _host_ já normalizado contra lista de permissão explícita.                              |
| A03 | **Session ID in URL Rewrite**                | Alerta laranja (Medium), classificado como _Systemic_ no painel do OWASP ZAP em `05-zap-alertas-gerados.png`, evidenciando o identificador de sessão presente na _query string_ das requisições.                                     | O identificador de sessão fica exposto em histórico de navegador, logs de servidor e proxies e no cabeçalho `Referer`, permitindo que um atacante reutilize a sessão de um usuário autenticado sem conhecer sua senha. | CWE-598 (Uso do Método GET com Strings de Query Contendo Dados Sensíveis) e OWASP Top 10:2021 — A07: Identification and Authentication Failures. | Transportar o identificador exclusivamente por cookie com `HttpOnly`, `Secure` e `SameSite`, rejeitando qualquer sessão recebida por parâmetro de URL.                                           |

### 5.2.1 Análise Detalhada do Alerta 1 (A01)

#### Descrição do Achado

Durante a varredura ativa executada pelo OWASP ZAP, a ferramenta identificou que o endpoint de login/consulta da aplicação é vulnerável a **Injeção SQL (SQL Injection)**. Isso ocorre porque o interpretador SQL do servidor de banco de dados da aplicação-alvo não diferencia instruções de controle dos dados fornecidos pelo usuário no corpo da requisição ou nos parâmetros de URL, executando comandos maliciosos injetados diretamente na query.

#### Evidência Extraída

- **Severidade:** Alta (High) / Alerta Vermelho no OWASP ZAP.
- **Endpoint Afetado na Instância de Testes:** `/rest/user/login` (Parâmetro `email` e/ou `password`).
- **Payload de Ataque Exemplo:** `admin@juice-sh.op' OR 1=1 --` enviado no campo de login.
- **Comportamento Observado:** O servidor retornou status `200 OK` e um token de sessão válido para a conta de administrador da aplicação, mesmo sem o fornecimento de uma senha legítima, evidenciando o bypass completo da barreira de autenticação.

#### Impacto Real no Sistema Nexora

Se essa vulnerabilidade existisse em um ambiente de produção da plataforma Nexora, as consequências seriam catastróficas:

1. **Quebra de Confidencialidade (LGPD):** Um atacante poderia realizar a extração em massa (dump) do banco de dados, vazando credenciais, hashes de senhas e informações pessoais de alunos e instrutores.
2. **Quebra de Integridade:** Modificação arbitrária de registros acadêmicos (como alteração de notas de avaliações, falsificação de progresso em trilhas de cursos e geração fraudulenta de certificados oficiais).
3. **Prejuízo Financeiro:** Alteração de dados bancários de instrutores no faturamento e liberação fraudulenta de cursos pagos sem a transação correspondente.

#### Relação CWE / OWASP

- **CWE-89:** Neutralização Inadequada de Elementos Especiais em Comandos SQL (SQL Injection).
- **OWASP Top 10:2021:** Categoria **A03:2021 — Injection**.

#### Correção Técnica Proposta

Para mitigar em definitivo a vulnerabilidade de SQL Injection na plataforma Nexora, as seguintes medidas devem ser adotadas no código-fonte:

1. **Uso de Consultas Parametrizadas (Prepared Statements):**
   Garantir que todas as consultas ao banco de dados usem parâmetros em vez de concatenação direta de strings. Isso força o interpretador a tratar a entrada do usuário estritamente como dado, nunca como comando executável.

   _Exemplo seguro (Node.js/Node-Postgres):_

   ```javascript
   const query =
     "SELECT id, email, role FROM Users WHERE email = $1 AND password = $2";
   const values = [req.body.email, hashed_password];
   const result = await db.query(query, values);
   ```

2. **Uso de ORM (Object-Relational Mapping):**
   Utilizar mapeadores de banco de dados modernos (como Sequelize, Prisma ou Hibernate) que utilizam parametrização por padrão em seus métodos de busca e persistência de dados, evitando a escrita manual de queries SQL vulneráveis.

3. **Princípio do Menor Privilégio no Banco de Dados:**
   A conta de serviço do banco de dados utilizada pela aplicação Nexora não deve possuir permissões de superusuário (admin/root). Ela deve ser restrita estritamente aos privilégios necessários para as tabelas de uso diário (ex: `SELECT`, `INSERT`, `UPDATE`), impedindo comandos de manipulação estrutural como `DROP TABLE` ou acesso ao sistema operacional.

### 5.2.2 Análise Detalhada do Alerta 2 (A02)

#### Descrição do Achado

A varredura do OWASP ZAP classificou como alerta de severidade **alta** a
existência de um **Redirecionamento Externo (Open Redirect)** na aplicação-alvo.
A falha ocorre quando a aplicação recebe um endereço de destino por meio de um
parâmetro controlado pelo usuário e executa o redirecionamento sem verificar se
esse destino pertence ao próprio domínio ou a uma lista previamente autorizada.

O ponto crítico é que a requisição inicial parte de um endereço legítimo. Para o
usuário e para filtros de reputação de domínio, o vínculo aparente é com a
aplicação confiável, e não com o destino final.

#### Evidência Extraída

- **Severidade:** Alta (High) / Alerta vermelho no painel do OWASP ZAP.
- **Origem do achado:** Varredura ativa (_Active Scan_) sobre a instância local
  do OWASP Juice Shop em `http://localhost:3000`, executada em 09 de agosto de 2026.
- **Confirmação prática da exploração:** durante a varredura, a própria
  aplicação-alvo sinalizou a resolução do desafio **"Allowlist Bypass — Enforce
  a redirect to a page you are not supposed to redirect to"**, registrado na
  captura `06-efeito-do-ataque-no-juiceshop.png`. O desafio só é marcado como
  concluído quando um redirecionamento efetivamente escapa da lista de destinos
  permitidos, o que confirma a exploração e não apenas a detecção do alerta.
- **Comportamento observado:** o mecanismo de validação de destino foi
  contornado, permitindo que a requisição fosse redirecionada para um endereço
  fora dos destinos autorizados pela aplicação.
- **Registros visuais:** painel consolidado em `05-zap-alertas-gerados.png` e
  confirmação da exploração em `06-efeito-do-ataque-no-juiceshop.png`.

#### Impacto Real no Sistema Nexora

Caso essa condição existisse na plataforma Nexora, o impacto se somaria a um
risco já mapeado na Etapa 2:

1. **Ampliação do risco R03 (phishing):** o atacante distribuiria um endereço
   iniciado no domínio oficial da Nexora, que conduziria a uma página de login
   falsa. Isso contorna a principal orientação dada aos usuários — conferir o
   domínio do link — e reduz a eficácia dos controles de autenticação de e-mail
   definidos no requisito RS02.
2. **Vazamento de dados de sessão pelo cabeçalho `Referer`:** se o
   redirecionamento ocorresse a partir de uma página autenticada, o endereço de
   origem poderia ser transmitido ao site externo, expondo identificadores
   presentes na URL. Esse efeito se agrava pela condição descrita no alerta A03.
3. **Encadeamento com o risco R01:** credenciais obtidas por essa via alimentam
   diretamente o cenário de uso indevido de conta, incluindo o caso de abuso
   CA03, de desvio de repasses financeiros de instrutores.
4. **Dano reputacional:** endereços maliciosos associados ao domínio
   institucional podem comprometer a reputação de envio da Nexora e afetar a
   entrega de suas mensagens legítimas.

#### Relação CWE / OWASP

- **CWE-601:** Redirecionamento de URL para Site Não Confiável (_Open
  Redirect_).
- **OWASP Top 10:2021:** Categoria **A01:2021 — Broken Access Control**, que
  incorpora o redirecionamento não validado.
- **Referência complementar:** OWASP Unvalidated Redirects and Forwards Cheat
  Sheet.

#### Correção Técnica Proposta

1. **Eliminar o destino controlado pelo cliente.** Substituir a URL recebida por
   um identificador interno resolvido no servidor:

```javascript
// Destinos permitidos, resolvidos exclusivamente no servidor
const DESTINOS = {
  painel: "/dashboard",
  curso: "/cursos",
  certificados: "/certificados",
};

const destino = DESTINOS[req.query.destino] ?? "/";
return res.redirect(destino);
```

2. **Validar após a normalização, não antes.** O nome do desafio resolvido
   — _Allowlist Bypass_ — indica que a aplicação possuía uma lista de destinos
   permitidos, mas sua verificação foi contornada. A comparação deve incidir
   sobre o _host_ já normalizado e decodificado, nunca sobre o texto bruto da
   URL, e a lista deve ser de permissão explícita, jamais de bloqueio.

3. **Exigir confirmação explícita para saídas legítimas.** Quando o destino
   externo for parte do fluxo — como o retorno do gateway de pagamento —
   apresentar uma página intermediária informando o domínio de destino antes de
   prosseguir.

4. **Restringir o vazamento pelo cabeçalho `Referer`.** Aplicar a política
   `Referrer-Policy: strict-origin-when-cross-origin`, impedindo que o caminho
   completo da página de origem seja transmitido a terceiros.

   ### 5.2.3 Análise Detalhada do Alerta 3 (A03)

#### Descrição do Achado

O OWASP ZAP registrou o alerta **Session ID in URL Rewrite**, de severidade
**média** e classificado como _Systemic_, indicando que o identificador de
sessão trafega na _query string_ das requisições em vez de ser transportado
exclusivamente por cookie.

A classificação como sistêmica é relevante: o alerta não decorre de um endpoint
isolado, mas de uma característica do mecanismo de gerenciamento de sessão da
aplicação, afetando potencialmente todas as requisições autenticadas.

#### Evidência Extraída

- **Severidade:** Média (Medium) / Alerta laranja, marcado como _Systemic_ no
  painel do OWASP ZAP.
- **Origem do achado:** Varredura sobre a instância local do OWASP Juice Shop em
  `http://localhost:3000`, na sessão executada em 09 de agosto de 2026.
- **Condição identificada:** presença de parâmetro de identificação de sessão na
  URL das requisições registradas pela ferramenta. Por se tratar de achado
  sistêmico, a condição não se restringe a um único endpoint, e sim ao
  mecanismo de sessão adotado pela aplicação.
- **Alertas correlatos na mesma sessão:** _Cookie No HttpOnly Flag_ e
  _Information Disclosure — Information in Browser sessionStorage_, ambos
  relacionados à proteção inadequada de dados de sessão, o que reforça a
  natureza sistêmica do problema.
- **Registro visual:** painel consolidado de alertas em
  `05-zap-alertas-gerados.png`.

#### Impacto Real no Sistema Nexora

O identificador de sessão equivale, em efeito prático, à credencial do usuário
autenticado. Sua presença na URL o expõe em quatro locais fora do controle da
aplicação:

1. **Histórico e cache do navegador:** em computadores compartilhados —
   laboratórios, bibliotecas e ambientes corporativos, comuns entre o público de
   uma plataforma EAD — a sessão permanece recuperável após o uso.
2. **Registros de servidores e intermediários:** URLs completas costumam ser
   gravadas em logs de acesso, proxies e serviços de distribuição de conteúdo,
   ampliando o alcance da exposição e agravando a ameaça T18 da Etapa 1.
3. **Cabeçalho `Referer` enviado a terceiros:** ao carregar um recurso externo,
   o navegador pode transmitir a URL de origem, entregando o identificador a um
   domínio não relacionado. Combinado ao alerta A02, o vazamento deixa de
   depender de um recurso externo qualquer e pode ser induzido pelo atacante.
4. **Compartilhamento involuntário:** um aluno que copie o endereço de uma aula
   para enviar a um colega transmite junto sua própria sessão.

A consequência direta é o **sequestro de sessão sem conhecimento da senha**.
Isso agrava o risco R01 por uma via que os controles do requisito RS01 não
alcançam: a autenticação multifator protege o momento do login, mas não impede
o reúso de uma sessão já estabelecida. Se a conta pertencer a um instrutor ou
administrador, o atacante alcança as funções privilegiadas descritas nas
ameaças T22 e T23.

#### Relação CWE / OWASP

- **CWE-598:** Uso do Método GET com Strings de Query Contendo Informação
  Sensível.
- **CWE-384:** Fixação de Sessão (_Session Fixation_), condição relacionada
  quando o identificador pode ser imposto por parâmetro.
- **OWASP Top 10:2021:** Categoria **A07:2021 — Identification and
  Authentication Failures**.
- **Referência complementar:** OWASP Session Management Cheat Sheet.

#### Correção Técnica Proposta

1. **Transportar a sessão exclusivamente por cookie, com atributos de
   proteção.**

```javascript
res.cookie("sessionId", tokenSessao, {
  httpOnly: true, // inacessível a JavaScript, reduz o impacto de XSS
  secure: true, // transmitido apenas sobre HTTPS
  sameSite: "strict", // não enviado em requisições originadas por terceiros
  maxAge: 1000 * 60 * 30,
});
```

2. **Rejeitar sessões recebidas por parâmetro de URL.** A aplicação não deve
   aceitar identificador de sessão fora do cookie, sob nenhuma condição de
   compatibilidade. Aceitá-lo como alternativa preserva integralmente o vetor
   de ataque.

3. **Renovar o identificador na elevação de privilégio.** Emitir novo
   identificador imediatamente após o login e após a validação do segundo fator,
   invalidando o anterior. Essa medida trata a fixação de sessão e complementa
   diretamente a decisão de arquitetura DA01.

4. **Aplicar `Referrer-Policy` e limitar o tempo de vida da sessão.** Reduzir a
   janela de reúso por meio de expiração por inatividade e encerramento
   explícito no _logout_, com invalidação do lado do servidor e não apenas
   remoção do cookie no cliente.

## 5.3 Possíveis falsos positivos e alertas descartados

Esta seção deverá registrar, para cada alerta questionado ou descartado:

- identificação original da ferramenta;
- evidência analisada;
- razão técnica para confirmar, reclassificar ou descartar;
- limitação que impeça uma conclusão definitiva;
- necessidade de teste manual adicional, quando aplicável.

## 5.4 Limitações da verificação

- A Nexora não possui implementação executável versionada neste repositório.
- Não há evidência que permita afirmar a existência ou ausência de uma
  vulnerabilidade real na Nexora.
- Resultados obtidos em aplicação educacional deliberadamente vulnerável devem
  ser apresentados como resultados daquele ambiente, e não como falhas
  comprovadas da Nexora.

## 5.5 Itens necessários para concluir o relatório

## 5.5 Pendências e limitações

A verificação realizada nesta etapa foi executada exclusivamente sobre uma
instância local do OWASP Juice Shop, utilizada como ambiente de laboratório
por ser uma aplicação deliberadamente vulnerável e adequada à realização de
testes DAST.

Dessa forma, os alertas apresentados neste relatório representam achados
observados no ambiente de testes e não devem ser interpretados como
vulnerabilidades comprovadas da plataforma Nexora, que possui caráter
documental e não dispõe de uma implementação web executável neste repositório.

Além disso, nem todos os alertas identificados pelo OWASP ZAP foram submetidos
à análise aprofundada. O relatório concentra-se nos três achados selecionados
(A01, A02 e A03), considerados relevantes para os objetivos da etapa.

## 5.6 Considerações finais

## 5.6 Considerações finais

A verificação dinâmica realizada com o OWASP ZAP foi concluída em um ambiente
de laboratório baseado no OWASP Juice Shop, permitindo à equipe executar uma
varredura DAST real, registrar evidências da execução e analisar tecnicamente
os principais achados identificados.

Foram selecionados três alertas para análise detalhada: A01 — Injeção SQL,
A02 — Redirecionamento Externo (Open Redirect) e A03 — Session ID in URL
Rewrite. Para cada achado foram registradas a evidência observada, a relação
com classificações CWE e OWASP, os possíveis impactos e as respectivas
medidas de correção.

Os resultados demonstram, de forma prática, como uma ferramenta DAST pode
identificar comportamentos vulneráveis em uma aplicação web e como esses
resultados podem ser relacionados aos riscos e requisitos de segurança
definidos nas etapas anteriores do projeto.

Como a Nexora é uma aplicação fictícia e documental, os resultados obtidos no
Juice Shop não representam vulnerabilidades comprovadas da Nexora. Eles foram
utilizados como evidência experimental para demonstrar o processo de
verificação de segurança e apoiar as decisões de tratamento e prevenção
descritas no projeto.

Com isso, a Etapa 5 apresenta o ambiente utilizado, as evidências da
execução, os achados selecionados, suas análises técnicas, limitações e
respectivas recomendações de correção.
