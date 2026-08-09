# 7 — Pipeline DevSecOps e Apresentação em Vídeo

---

## 7.1 Atividades de Segurança no Pipeline DevSecOps

*(PARTE DA GABRIELA; favor, apagar depois de preencher)*

---

## 7.2 Condições de Bloqueio (Gates de Segurança)

| Gate | Fase | Condição de bloqueio | Evidência utilizada | Responsável pela análise | Condição para liberação |
| :---: | :--- | :--- | :--- | :--- |
| **G01** | Testes de segurança | Teste obrigatório de autenticação, autorização ou callback falha, incluindo TS03/TS04. | Relatório com teste, entrada, esperado e obtido. | Desenvolvimento e responsável técnico. | Corrigir, executar a suíte afetada novamente e anexar aprovação. |
| **G02** | SAST/SCA/DAST | Vulnerabilidade crítica, ou alta explorável, sem análise, responsável e tratamento. | Relatório e triagem com severidade e alcance. | Segurança, desenvolvimento e responsável pelo componente. | Corrigir/mitigar e revalidar; exceção exige justificativa, prazo e controle compensatório. |
| **G03** | Commit e integração contínua | Scanner identifica segredo válido no repositório, artefato ou log de build. | Relatório mascarado do scanner. | Desenvolvimento e infraestrutura. | Remover, revogar/rotacionar a credencial e repetir a varredura. |
| **G04** | Preparação da entrega | Artefato obrigatório sem evidência verificável. | Checklist do repositório e validação de caminhos. | Responsável da etapa e coordenação. | Adicionar/corrigir, validar e revisar o artefato. |

Os gates exigem evidência, responsável e decisão registrada; uma exceção não pode apenas ignorar um alerta.

---

## 7.3 Considerações Finais e Aprendizados do Grupo

STRIDE e os casos de abuso transformaram fluxos de autenticação, pagamentos, conteúdo e administração em riscos comparáveis. A priorização orienta requisitos verificáveis, como MFA, limitação de tentativas e validação de callbacks, que por sua vez permitem definir testes antes da implementação.

Testes, verificação de vulnerabilidades e revisão humana são complementares: testes validam comportamentos conhecidos; ferramentas revelam achados a triar; e evidências reais são necessárias para afirmar vulnerabilidades ou eficácia de controles. Logs minimizados e regras de detecção apoiam resposta a falhas preventivas sem expor dados sensíveis.

O DevSecOps conecta essa rastreabilidade aos gates de entrega. Limitações atuais incluem ausência de implementação completa e de resultados reais de verificação; melhorias futuras incluem automatizar validações, calibrar regras com métricas reais e revisar periodicamente riscos e controles.

---

## 7.4 Roteiro e Script do Vídeo Final de Apresentação

*(PARTE DO ERIK; favor, apagar depois de preencher)*

---

## 7.5 Entrega do Vídeo Final

*(PARTE DA BRUNA; favor, apagar depois de preencher)*
