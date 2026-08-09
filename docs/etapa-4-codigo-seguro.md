# 4 — Código Seguro e Testes de Segurança

Esta etapa deverá demonstrar duas práticas de código seguro derivadas dos
riscos e requisitos das etapas anteriores. Os testes são definidos antes da
descrição da implementação.

## 4.1 Prática 1 — autenticação reforçada e limitação de tentativas

**Risco/Requisito:** R01 e RS01. Para Instrutor e Administrador, exigir MFA; para todo login, limitar cinco falhas por IP/conta em 15 minutos e devolver `429` na sexta. A senha é comparada somente contra hash seguro; a decisão de papel é do servidor.

| ID | Cenário | Resultado esperado |
|---|---|---|
| TS01 | Login válido de administrador, senha correta e segundo fator válido | Sessão autenticada, papel carregado do servidor e evento de sucesso sem senha, OTP ou token em log. |
| TS02 | Seis tentativas inválidas no intervalo por mesma conta/IP | A sexta é limitada (`429`), não cria sessão, registra evento minimizado e dispara a regra RD01. |

Esta prática é distinta da Prática 2: protege autenticação e tomada de conta; a segunda valida uma integração externa de pagamento.

## 4.2 Prática 2 — Validação de callbacks de pagamento

**Situação:** proposta provisória até a confirmação da Prática 1.

**Risco relacionado:** R04 — Forjamento de notificações de callback de
pagamento.

**Requisito relacionado:** RS03 — Verificação da assinatura HMAC-SHA256 antes
da liberação de qualquer curso.

**Justificativa:** callbacks são entradas externas e não podem ser considerados
confiáveis apenas porque utilizam o endpoint esperado. A API deve verificar a
autenticidade e a integridade da notificação, relacioná-la a um pedido existente
e impedir processamento repetido antes de alterar o estado da compra ou liberar
conteúdo. A prática é sustentada pela [OWASP Third Party Payment Gateway
Integration Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Third_Party_Payment_Gateway_Integration_Cheat_Sheet.html).

### 4.2.1 Casos de teste definidos antes da implementação

| ID | Tipo de cenário | Entrada ou ação | Resultado seguro esperado |
| :---: | :--- | :--- | :--- |
| **TS03** | Válido | O gateway envia uma única requisição `POST /api/v1/payments/callback` para um pedido pendente, com assinatura HMAC válida e valores de pedido, moeda e transação correspondentes aos registros do servidor. | A API valida a assinatura com comparação segura, consulta ou confirma o estado da transação, registra o identificador e o resultado sem armazenar chave ou assinatura sensível, altera o pedido para pago uma única vez e libera somente o curso associado. A resposta confirma o processamento sem expor dados internos. |
| **TS04** | Malicioso ou não autorizado | Um agente envia callback sem assinatura, com HMAC inválido, pedido inexistente, valor divergente ou identificador de transação já processado. | A API rejeita a requisição antes de alterar o pedido ou liberar conteúdo. Assinatura ausente ou inválida produz resposta `401 Unauthorized`; divergência ou repetição é recusada de forma idempotente. O evento é registrado com horário, pedido, origem e motivo sanitizado, sem segredo HMAC nem dados financeiros completos, e fica disponível para alerta e investigação. |

### 4.2.2 Critérios de aprovação

- TS03 deve comprovar uma transição única e rastreável de pedido pendente para
  pago.
- TS04 deve comprovar que nenhuma variação inválida libera curso ou altera o
  estado financeiro.
- Os registros devem permitir auditoria sem expor chaves, assinaturas ou dados
  financeiros completos.
- A futura implementação ou pseudocódigo pertence ao Pacote D e não faz parte
  desta seção.

### 4.2.3 Pendência para consolidação

Se a Prática 1 também utilizar validação de entrada ou callbacks de pagamento,
o responsável pelo Pacote C deverá escolher outra prática relacionada a um
requisito existente e atualizar TS03 e TS04 antes do merge.
