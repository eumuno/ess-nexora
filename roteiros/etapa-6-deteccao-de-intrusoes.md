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

## 6.2 Dependências dos demais pacotes

- A fundamentação sobre prevenção, detecção e eventos gerais de log pertence ao
  Pacote A.
- A Regra 3 e o fluxo completo de resposta pertencem ao Pacote C.
- A consolidação e ordenação final do roteiro pertencem ao Pacote D.
