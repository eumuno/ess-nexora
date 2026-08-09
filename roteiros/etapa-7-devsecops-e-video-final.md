# 7 — DevSecOps e Vídeo Final

## 7.1 Condições de Bloqueio do Pipeline

Os gates abaixo impedem a continuidade quando faltam evidências mínimas ou
quando uma falha relevante permanece sem decisão registrada. Eles deverão ser
integrados ao pipeline definido pelo Pacote A.

| Gate | Fase | Condição de bloqueio | Evidência utilizada | Responsável pela análise | Condição para liberação |
| :---: | :--- | :--- | :--- | :--- | :--- |
| **G01** | Testes de segurança | Qualquer teste obrigatório relacionado a autenticação, autorização ou callback de pagamento falha, incluindo TS03 ou TS04 quando incorporados à suíte. | Relatório automatizado com identificador do teste, entrada, resultado esperado e resultado obtido. | Desenvolvimento e responsável técnico pela segurança. | Corrigir a causa, executar novamente toda a suíte afetada e anexar relatório aprovado. Não liberar por simples repetição sem correção ou justificativa. |
| **G02** | Análise de código, dependências ou verificação dinâmica | Vulnerabilidade crítica, ou alta explorável no contexto da Nexora, permanece sem análise, responsável e estratégia de tratamento. | Relatório SAST/SCA/DAST, evidência do achado e registro de triagem com severidade e alcance. | Segurança, desenvolvimento e responsável pelo componente. | Corrigir ou mitigar e validar novamente; excepcionalmente, registrar aceitação formal com justificativa, prazo, responsável e controle compensatório. |
| **G03** | Commit e integração contínua | Scanner identifica senha, token, chave HMAC, chave privada ou outro segredo válido no repositório, artefato ou log de build. | Relatório do scanner de segredos com caminho e tipo do achado, mantendo o valor mascarado. | Desenvolvimento e infraestrutura. | Remover o segredo do conteúdo distribuído, revogar ou rotacionar a credencial, revisar o histórico exposto e repetir a varredura sem achados válidos. |
| **G04** | Preparação da entrega | Artefato obrigatório da etapa não existe ou não possui evidência verificável, como relatório de testes, fonte de diagrama, análise de alerta ou registro de decisão. | Checklist do repositório e validação de links e caminhos dos artefatos. | Responsável pela etapa e coordenação do grupo. | Adicionar ou corrigir o artefato, validar seus caminhos e obter revisão do responsável antes de prosseguir. |

### 7.1.1 Aplicação dos gates

Os gates devem produzir resultado objetivo, responsável e evidência. Uma
exceção não pode consistir apenas em ignorar o alerta: ela precisa registrar o
risco aceito, justificativa, prazo de revisão e controles compensatórios. G01
protege comportamentos especificados antes da implementação; G02 impede que
achados graves desapareçam entre relatórios; G03 reduz a exposição direta de
credenciais; e G04 garante que a rastreabilidade acadêmica e técnica esteja
completa.

## 7.2 Considerações Finais e Aprendizados

A modelagem STRIDE mostrou que analisar apenas funcionalidades esperadas não é
suficiente: os mesmos fluxos de autenticação, pagamentos, conteúdo e
administração precisam ser examinados sob a perspectiva de uso malicioso. Os
casos de abuso tornam essas ameaças concretas, enquanto a avaliação de riscos
ajuda a comparar probabilidade, impacto e urgência em vez de tratar todos os
cenários como equivalentes.

A transformação dos riscos em requisitos verificáveis conecta a análise ao
projeto da arquitetura. Critérios mensuráveis, como exigir MFA, limitar
tentativas e validar callbacks, permitem definir testes antes da implementação
e deixam explícito o comportamento seguro esperado. O mapeamento para CWE e
OWASP também fornece vocabulário e referências reconhecidas para justificar as
decisões.

Os testes antecipados, a verificação de vulnerabilidades e a coleta de
evidências cumprem papéis complementares. Testes demonstram comportamentos
esperados conhecidos; ferramentas de análise podem revelar condições não
previstas; e a revisão humana é necessária para contextualizar severidade,
descartar falsos positivos e propor correções. Sem evidências reais, não se deve
afirmar que um controle foi implementado ou que uma vulnerabilidade foi
confirmada.

Logs e regras de detecção reconhecem que controles preventivos podem falhar.
Eventos suficientes, minimizados e protegidos permitem identificar tentativas
de invasão de conta, callbacks falsos e outras anomalias sem transformar os
próprios registros em fonte de exposição. Limiares deverão ser calibrados com
o uso real para equilibrar detecção e falsos positivos.

Por fim, o pipeline DevSecOps integra essas decisões ao fluxo de entrega. Gates
objetivos impedem que testes reprovados, vulnerabilidades graves sem análise,
segredos expostos ou evidências ausentes avancem silenciosamente. As principais
limitações atuais são a ausência de uma implementação completa da Nexora, a
dependência de atividades distribuídas entre os integrantes e a falta, até o
momento, de resultados reais de verificação. Como melhoria futura, o grupo
deverá automatizar validações, calibrar regras com métricas reais, revisar
periodicamente riscos e manter a rastreabilidade entre ameaça, risco,
requisito, teste, evidência, detecção e decisão de entrega.

## 7.3 Dependências dos demais pacotes

- A tabela completa do pipeline e suas fases pertence ao Pacote A.
- O roteiro de 5 a 8 minutos pertence ao Pacote C.
- A coordenação, edição e publicação do vídeo pertencem ao Pacote D.
