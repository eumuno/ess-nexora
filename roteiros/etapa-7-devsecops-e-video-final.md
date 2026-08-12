# 7 — Pipeline DevSecOps e Apresentação em Vídeo

---

## 7.1 Atividades de Segurança no Pipeline DevSecOps

Para garantir que a segurança não seja um componente adicionado tardiamente ao software, a plataforma Nexora adota a filosofia DevSecOps. O ciclo de vida do desenvolvimento de software (SDLC) foi reestruturado para incorporar controles, verificações e validações de segurança automatizadas e manuais em cada uma de suas oito fases principais.

A tabela a seguir mapeia cada momento do pipeline, as atividades de segurança correspondentes, as evidências geradas (conectando com o trabalho prático realizado pelo grupo nas etapas anteriores) e os critérios mínimos de continuidade para que o pipeline avance de forma segura.

### Tabela Mestra de Atividades de Segurança do Pipeline

| Momento / Fase do SDLC | Atividade de Segurança Realizada | Evidência Técnica Produzida | Condição Mínima de Continuidade |
| :--- | :--- | :--- | :--- |
| **1. Planejamento** | Modelagem de ameaças e mapeamento dos principais Casos de Abuso relacionados ao negócio. | Documento Markdown de análise STRIDE contendo as ameaças e comportamentos de ataque modelados. | 100% dos ativos críticos identificados e ameaças de alto impacto mapeadas pelo grupo. |
| **2. Requisitos e Arquitetura** | Definição de requisitos de segurança baseados no NIST CSF 2.0 e tomada de decisões arquiteturais seguras. | Registro de riscos priorizados, requisitos de segurança formais e diagrama da arquitetura segura. | Plano de tratamento de riscos aprovado e fronteiras de confiança da arquitetura claramente estabelecidas. |
| **3. Implementação (Código)** | Desenvolvimento defensivo baseado em guias oficiais (OWASP Cheat Sheets) para mitigar falhas comuns. | Código-fonte ou pseudocódigo estruturado contendo as práticas defensivas implementadas. | Zero credenciais, chaves de API ou segredos de ambiente expostos em texto simples no código-fonte. |
| **4. Testes** | Criação e execução de planos de testes funcionais e testes de segurança focados nos fluxos críticos. | Especificações técnicas de casos de teste de segurança positivos e negativos documentados. | 100% dos testes de regressão de segurança (ex: validação de token MFA e bloqueio por força bruta) aprovados. |
| **5. Análise de Código (SAST/SCA)** | Varredura estática de vulnerabilidades no código (SAST) e análise de vulnerabilidades em bibliotecas de terceiros (SCA). | Relatórios automatizados gerados por ferramentas de varredura estática e análise de dependências desatualizadas. | Ausência de dependências de terceiros com vulnerabilidades conhecidas (CVEs) críticas sem mitigação ativa. |
| **6. Verificação Dinâmica (DAST)** | Varredura de segurança dinâmica ativa e passiva simulando ataques em ambiente controlado de homologação. | Relatório consolidado contendo os alertas de segurança gerados pela ferramenta de varredura ativa. | Mitigação e correção técnica validada de todos os alertas classificados com severidade Crítica ou Alta. |
| **7. Implantação (Deployment)** | Provisionamento seguro de servidores, hardening de contêineres e deploy automatizado via CI/CD. | Scripts de automação de infraestrutura revisados e certificados criptográficos SSL/TLS configurados. | Build de produção gerado com sucesso sem avisos de configuração incorreta ou portas administrativas expostas. |
| **8. Operação e Monitoramento** | Registro contínuo de logs de auditoria estruturados e aplicação de regras de correlação de alertas. | Trilha de auditoria em JSON centralizada e roteiro de resposta rápida pós-alertas de segurança. | Metadados de logs obrigatórios integrados de forma inviolável e servidores de logs protegidos contra exclusão. |

---

## 7.2 Condições de Bloqueio (Gates de Segurança)

| Gate | Fase e bloqueio | Evidência | Análise e liberação |
| :---: | :--- | :--- | :--- |
| **G01** | **Testes:** falha em teste obrigatório de autenticação, autorização ou callback (TS03/TS04). | Relatório com teste, entrada, resultado esperado e obtido. | **Desenvolvimento e responsável técnico.** Corrigir, repetir a suíte afetada e anexar aprovação. |
| **G02** | **SAST/SCA/DAST:** vulnerabilidade crítica, ou alta explorável, sem análise e tratamento. | Relatório do scanner e triagem com severidade e alcance. | **Segurança, desenvolvimento e responsável pelo componente.** Corrigir/mitigar e revalidar; exceção exige justificativa, prazo e controle compensatório. |
| **G03** | **Commit/CI:** scanner identifica segredo válido no repositório, artefato ou log de build. | Relatório mascarado do scanner. | **Desenvolvimento e infraestrutura.** Remover o segredo, revogar/rotacionar a credencial e repetir a varredura. |
| **G04** | **Entrega:** artefato obrigatório sem evidência verificável. | Checklist e validação de caminhos. | **Responsável da etapa e coordenação.** Adicionar/corrigir, validar e revisar o artefato. |

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

Esta seção apresenta a entrega oficial da apresentação em vídeo do projeto Nexora, consolidando todo o trabalho de modelagem de ameaças, análise de riscos, arquitetura segura, práticas de código, verificação e detecção de intrusões desenvolvido ao longo da disciplina.

### 7.5.1 Ficha Técnica da Produção e Coordenação
*   **Coordenação e Edição de Vídeo:** Bruna Rosa Ferreira
*   **Software de Edição Utilizado:** CapCut Desktop
*   **Plataforma de Hospedagem:** YouTube (Modo Não-Listado)
*   **Duração Planejada/Final:** A PREENCHER
*   **Formato de Gravação:** Capturas de tela individuais com áudio dos integrantes, unificados em uma linha do tempo única.

### 7.5.2 Divisão de Participações no Vídeo
Para garantir que a avaliação individual seja justa e rastreável pelo professor Silvio, a apresentação foi dividida conforme a contribuição de cada integrante nas etapas do projeto (ESSA PARTE AINDA PODE SER MODIFICADA DEPOIS, GALERINHA):
1.  **Introdução e Apresentação do Sistema (Nexora):** Bruna
2.  **Modelagem de Ameaças (STRIDE) e Análise de Riscos (NIST):** Gabriela e Inaurrara
3.  **Arquitetura Segura e Decisões de Projeto:** Erik
4.  **Código Seguro e Casos de Teste (MFA / Validação de Callback):** Bruna e Erik
5.  **Verificação de Vulnerabilidades (OWASP ZAP / Juice Shop):** Bruna e Gabriela
6.  **Monitoramento, Regras de Detecção e Resposta:** Bruna e Inaurrara
7.  **Pipeline DevSecOps, Gates de Segurança e Aprendizados:** Gabriela, Inaurrara e Erik

---

### 7.5.3 Links e Evidência de Visualização

*   **🎬 LINK DO VÍDEO NO YOUTUBE (NÃO-LISTADO):** [Clique aqui para assistir à apresentação do Projeto Nexora (AINDA Ñ FOI GRAVADO)](#)
*   **📂 PASTA DE EVIDÊNCIAS DE SETUP:** `evidencias/etapa-5/`
