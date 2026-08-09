# 3 — Requisitos, Decisões de Arquitetura e Diagrama Seguro
Essa etapa tem como propósito transformar os riscos e controles identificados anteriormente em requisitos de segurança específicos e decisões de projeto. O objetivo principal é planejar a organização da plataforma Nexora para garantir a mitigação dos riscos prioritários de forma estruturada antes de avançar para a fase de codificação.

## 3.1 Requisitos de Segurança e Critérios de Verificação

| ID | Risco de Origem | Requisito de Segurança | Critério de Verificação |
| :--- | :--- | :--- | :--- |
| **RS01** | **R01 — Uso indevido de conta (Credential Stuffing)** | O sistema de autenticação na API da Nexora deve exigir autenticação multifator (MFA) obrigatória para contas com perfil de Instrutor e Administrador, e aplicar um mecanismo de limitação de taxa (Rate Limiting) bloqueando acessos após 5 tentativas de login consecutivas inválidas vindas do mesmo IP em menos de 15 minutos. | 1. Requisições de autenticação para perfis de Instrutor ou Administrador sem o envio do segundo fator (MFA) válido devem ser rejeitadas com status HTTP 401 Unauthorized.<br>2. A partir da 6ª tentativa consecutiva de login falha vinda do mesmo IP dentro de 15 minutos, o servidor de API deve recusar o processamento e retornar status HTTP 429 Too Many Requests. |
| **RS02** | **R03 — Ataques de phishing com e-mails falsos** | Todos os e-mails enviados em nome do domínio institucional @nexora.com devem ser autenticados na origem através da implementação e publicação correta das diretivas de segurança DNS: SPF (Sender Policy Framework), DKIM (DomainKeys Identified Mail) e uma política DMARC configurada estritamente com diretiva de rejeição (p=reject). | 1. Consultas públicas de registro DNS TXT para o domínio nexora.com devem retornar chaves públicas e diretivas SPF válidas autorizando os IPs dos servidores oficiais de envio.<br>2. A consulta DMARC deve retornar o parâmetro de política ativo em modo rígido como v=DMARC1; p=reject;, instruindo servidores de destino a rejeitarem e-mails não assinados. |
| **RS03** | **R04 — Forjamento de notificações de callback de pagamento** | O endpoint da API responsável pelo recebimento de notificações pós-venda enviadas pelo gateway de pagamento (/api/v1/payments/callback) deve verificar obrigatoriamente a assinatura criptográfica digital HMAC-SHA256 enviada nos cabeçalhos da requisição, calculando localmente o hash com base na chave secreta compartilhada antes de liberar qualquer curso. | 1. Requisições POST de callback recebidas sem a assinatura HMAC correspondente ou contendo um hash inválido devem ser rejeitadas imediatamente com erro HTTP 401 Unauthorized, sem que o curso associado seja liberado.<br>2. Tentativas de chamada ao endpoint sem verificação devem ser registradas nos logs do sistema como alertas de segurança de alta criticidade. |

## 3.2 Mapeamento de Vulnerabilidades

O mapeamento relaciona os requisitos já definidos a fraquezas catalogadas pela
CWE e a orientações oficiais da OWASP. As referências descrevem categorias de
fraqueza aplicáveis ao projeto; elas não afirmam que exista uma vulnerabilidade
implementada e comprovada, pois a Nexora ainda está em fase de especificação.

| Risco | Requisito | Vulnerabilidade ou categoria | Referência oficial | Relação com a Nexora |
| :--- | :--- | :--- | :--- | :--- |
| **R01** | **RS01** | **CWE-307 — Improper Restriction of Excessive Authentication Attempts** e **CWE-308 — Use of Single-factor Authentication** | [CWE-307](https://cwe.mitre.org/data/definitions/307.html), [CWE-308](https://cwe.mitre.org/data/definitions/308.html) e [OWASP Multifactor Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Multifactor_Authentication_Cheat_Sheet.html) | Sem limitação de tentativas, o serviço de autenticação permite automatizar o teste de credenciais reutilizadas. Se contas privilegiadas dependerem apenas de senha, o comprometimento desse único fator é suficiente para autenticação completa. O MFA e o bloqueio mensurável do RS01 tratam diretamente essas duas condições. |
| **R03** | **RS02** | **CWE-290 — Authentication Bypass by Spoofing** | [CWE-290](https://cwe.mitre.org/data/definitions/290.html) | A ausência ou configuração inadequada de SPF, DKIM e DMARC dificulta que servidores destinatários diferenciem mensagens autorizadas das que falsificam a identidade do domínio. O RS02 estabelece autenticação da origem e política de rejeição para reduzir a aceitação de mensagens que se apresentem indevidamente como enviadas pela Nexora. |
| **R04** | **RS03** | **CWE-346 — Origin Validation Error** e **CWE-354 — Improper Validation of Integrity Check Value** | [CWE-346](https://cwe.mitre.org/data/definitions/346.html), [CWE-354](https://cwe.mitre.org/data/definitions/354.html) e [OWASP Third Party Payment Gateway Integration Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Third_Party_Payment_Gateway_Integration_Cheat_Sheet.html) | Um callback aceito sem validar a origem e sem comparar corretamente a assinatura recebida com o HMAC calculado pode ser forjado ou alterado. O RS03 exige essa verificação antes da liberação do curso; a orientação da OWASP também recomenda autenticar callbacks, confirmar o pagamento no servidor e relacionar valor, moeda e pedido. |

### Rastreabilidade da seção

| Ameaça | Risco | Requisito | Vulnerabilidade ou categoria |
| :---: | :---: | :---: | :--- |
| T01 | R01 | RS01 | CWE-307 e CWE-308 |
| T03 | R03 | RS02 | CWE-290 |
| T04 | R04 | RS03 | CWE-346 e CWE-354 |
