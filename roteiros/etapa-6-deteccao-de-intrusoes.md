# 6 — Monitoramento e Detecção de Intrusões

## 6.1 Regras de Detecção 1 e 2

As regras abaixo utilizam riscos e requisitos já definidos nas Etapas 2 e 3.
Os limiares são valores iniciais de projeto e deverão ser calibrados com dados
reais de uso para reduzir falsos positivos.

| Regra | Risco observado | Fonte de dados | Condição objetiva de alerta | Resposta inicial |
| :---: | :--- | :--- | :--- | :--- |
| **RD01** | **R01 / T01 / CA03 — credential stuffing e sequestro de conta** | Logs do serviço de autenticação e do mecanismo de *rate limiting*: horário, IP, conta, resultado, perfil e identificador de sessão, sem registrar senhas ou códigos MFA. | Gerar alerta de alta severidade quando um mesmo IP acumular falhas de login em **5 ou mais contas distintas dentro de 10 minutos**, ou quando uma conta de Instrutor ou Administrador atingir **5 falhas consecutivas em 15 minutos**. | Aplicar limitação temporária ao IP e à conta conforme RS01, preservar os eventos correlacionados, notificar a equipe de segurança e avisar o titular quando houver tentativa contra conta privilegiada. Não bloquear permanentemente sem análise. |
| **RD02** | **R04 / T04 — forjamento de callback de pagamento** | Logs do endpoint de callback, verificação HMAC, consulta ao gateway e estado do pedido: horário, origem, pedido, transação, resultado da assinatura e motivo da rejeição, sem registrar chave secreta ou dados financeiros completos. | Gerar alerta de alta severidade quando ocorrer **qualquer callback com HMAC inválido para pedido existente** ou **3 callbacks rejeitados da mesma origem ou para o mesmo pedido em 5 minutos** por assinatura ausente, divergência de valor ou repetição de transação. | Impedir a liberação do curso, manter o pedido no estado anterior, limitar temporariamente a origem quando houver repetição, preservar evidências e encaminhar o alerta para validação da integração e consulta do status diretamente no gateway. |

### 6.1.1 RD01 — Justificativa, logs e limitações

O *credential stuffing* distribui tentativas entre contas, por isso RD01 não
observa somente falhas consecutivas de um usuário. A combinação de várias
contas atingidas por uma mesma origem com um limiar específico para perfis
privilegiados permite detectar tanto automação horizontal quanto ataques
direcionados.

Proxies corporativos, redes universitárias, NAT e usuários que esqueceram a
senha podem gerar falsos positivos. A resposta inicial é temporária e deve
correlacionar IP, conta, dispositivo e histórico antes de qualquer bloqueio
duradouro. Senhas e códigos MFA nunca devem aparecer nos logs.

### 6.1.2 RD02 — Justificativa, logs e limitações

Uma única assinatura inválida para um pedido real já é relevante porque pode
representar tentativa de falsificação, erro de chave ou defeito na integração.
A repetição em curto intervalo aumenta a confiança de que existe abuso ou
falha operacional persistente.

Rotação de chaves, relógios dessincronizados e implantação incorreta do gateway
podem gerar falsos positivos. A resposta preserva o pedido e exige confirmação
por canal servidor a servidor antes de liberar conteúdo. A chave HMAC, a
assinatura completa e dados financeiros sensíveis não devem ser registrados.

## 6.2 Eventos e Regra 3

Eventos mínimos: horário UTC, tipo da ação, conta ou identificador pseudonimizado, IP/origem, resultado, recurso afetado, correlação da requisição e motivo sanitizado. Não registrar senha, token, segredo HMAC, CPF completo ou cartão.

**RD03 — alteração financeira suspeita:** gerar alerta alto quando uma conta de instrutor alterar chave Pix/dados bancários até 30 minutos após login em dispositivo novo, ou quando houver duas alterações em 24 horas. Conter temporariamente o repasse, exigir reautenticação/MFA, preservar logs e confirmar a alteração com o titular por canal independente.

### Fluxo de resposta

1. Receber e classificar o alerta; correlacionar conta, origem e recurso.
2. Validar se é incidente, erro operacional ou falso positivo.
3. Conter: limitar origem, revogar sessão ou suspender a ação financeira, proporcionalmente.
4. Preservar evidências e registrar decisão, responsável e horário.
5. Erradicar/corrigir a causa, recuperar o estado seguro e avisar os afetados quando aplicável.
6. Encerrar somente após revisão, ajuste do controle/regra e registro de lições aprendidas.

## 6.3 Consolidação

As três regras cobrem tomada de conta, fraude de callback e desvio de repasse. Os limiares são iniciais e devem ser calibrados com dados reais.
