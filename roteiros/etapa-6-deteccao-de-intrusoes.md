# 6 — Monitoramento e Detecção de Intrusões

Este roteiro organiza controles de detecção para os riscos priorizados da Nexora.

---

## 6.1 Fundamentação de Detecção de Intrusões

*(PARTE DA GABRIELA; favor, apagar depois de preencher)*

---

## 6.2 Eventos de Log Necessários na Nexora

*(PARTE DA GABRIELA; favor, apagar depois de preencher)*

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
| **Risco observado** | R07 / T03 / CA04 — Vazamento de informações e exposição indevida de dados pessoais de alunos (como CPF e e-mail) por manipulação de parâmetros. |
| **Fonte de dados** | Logs de requisições HTTP do API Gateway e do Servidor Web (direcionados ao endpoint `/api/v1/users/perfil`), contendo ID do usuário autenticado, IP de origem, cabeçalhos de requisição e status de retorno HTTP. |
| **Condição de alerta** | Alerta de **prioridade alta** quando uma mesma credencial de usuário autenticada (ou um mesmo IP não autenticado de Visitante) realizar requisições para **mais de 30 IDs de perfis diferentes em um intervalo menor que 2 minutos**. |
| **Resposta inicial** | Revogação imediata do token de sessão ativo (JWT) do usuário requisitante, bloqueio temporário do IP de origem no API Gateway por 1 hora e registro do evento no servidor de auditoria para investigação detalhada. |

### 6.4.1 Justificativa e Limitações da RD03
A regra **RD03** é essencial para detectar comportamentos automatizados de raspagem de dados (*scraping*) antes que um atacante consiga mapear e extrair dados em massa de toda a base de estudantes da Nexora.

Como limitação operacional, comportamentos legítimos de navegação rápida por páginas do fórum de dúvidas ou perfis de instrutores podem gerar potenciais falsos positivos. Por isso, o limiar de 30 requisições em 2 minutos é calibrado para capturar apenas requisições em velocidades e volumes característicos de ferramentas automatizadas, e não de navegação humana comum.

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

*(PARTE DO ERIK; favor, apagar depois de preencher)*
