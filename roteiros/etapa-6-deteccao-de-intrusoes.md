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

*(PARTE DA BRUNA; favor, apagar depois de preencher)*

---

## 6.5 Roteiro de Resposta pós-Alerta

*(PARTE DA BRUNA; favor, apagar depois de preencher)*

---

## 6.6 Setup e Estrutura do Roteiro

*(PARTE DO ERIK; favor, apagar depois de preencher)*
