# 5 — Verificação de Vulnerabilidades

Este documento constitui o **Relatório de Verificação de Vulnerabilidades**, atendendo às exigências da Etapa 5 do trabalho prático. O escopo desta atividade consiste na realização de testes de segurança dinâmicos (DAST) em uma aplicação controlada, analisando possíveis falhas de segurança por meio de varreduras automatizadas, interpretando seus alertas e propondo correções.

---

## 5.1 Ambiente e Configuração de Varredura
### 5.1.1 Justificativa do Ambiente de Laboratório

A plataforma **Nexora** é um projeto de software concebido de maneira **100% fictícia e documental**, o que significa que o grupo não possui uma implementação web executável em ambiente de produção para ser testada diretamente.

Para suprir essa limitação técnica e cumprir os objetivos pedagógicos da disciplina, a equipe estabeleceu um ambiente de laboratório seguro utilizando o **OWASP Juice Shop**. O Juice Shop é uma aplicação web de código aberto e deliberadamente vulnerável, desenvolvida para fins de treinamento e educação em segurança de aplicações web.

Essa abordagem permite que o grupo execute uma sessão de varredura dinâmica real, avalie as saídas de segurança de uma ferramenta de DAST e desenvolva as análises técnicas de risco necessárias para a consolidação deste relatório.

### 5.1.2 Especificação Técnica do Ambiente de Testes

Para garantir a reprodutibilidade dos testes e a validade científica das evidências, o setup do laboratório foi configurado com os seguintes parâmetros técnicos:

*   **Sistema Operacional da Máquina de Testes:** Windows 11
*   **Ambiente de Execução de Containers:** Docker Desktop (v4.85.0)
*   **Aplicação Alvo (Testada):** OWASP Juice Shop (v20.1.1)
*   **Endereço de Acesso Local:** `http://localhost:3000`
*   **Porta de Comunicação:** 3000
*   **Ferramenta de Varredura Ativa (DAST):** OWASP ZAP (ZAP Version: 2.17.0)
*   **Escopo do Teste:** Restrito estritamente à aplicação Juice Shop rodando localmente na porta de testes da equipe, sem interações com serviços de terceiros externos ou sistemas públicos de produção.
*   **Data de Execução da Varredura:** 09 de agosto de 2026
*   **Responsável pela Execução:** Bruna Rosa Ferreira

---

### 5.1.3 Evidências da Execução (Varredura de Segurança)

Os registros visuais a seguir comprovam a configuração do ambiente de laboratório, a execução da varredura automatizada pelo OWASP ZAP e os alertas identificados durante a sessão de testes. Todos os arquivos estão salvos na pasta de evidências do repositório.

#### 1. Aplicação-Alvo em Execução

O container Docker do OWASP Juice Shop foi iniciado e disponibilizado localmente na porta 3000.
![01 - Juice Shop Executando](../evidencias/etapa-5/01-juice-shop-executando.png)

#### 2. Configuração de Escopo e Alvo no OWASP ZAP

Configuração do OWASP ZAP apontando a varredura para a instância local do Juice Shop.
![02 - ZAP Configuração Básica](../evidencias/etapa-5/02-zap-configuracao-basica.png)

#### 3. Início da Varredura e Mapeamento da Aplicação

Registro do início da sessão de testes realizada pelo OWASP ZAP, incluindo o processo inicial de identificação e mapeamento dos recursos da aplicação.
![03 - ZAP Varredura Iniciando](../evidencias/etapa-5/03-zap-varredura-iniciando.png)

#### 4. Varredura Dinâmica em Progresso (Active Scan)

Registro da execução do Active Scan do OWASP ZAP sobre a aplicação-alvo, durante a qual a ferramenta realizou requisições automatizadas para verificar possíveis vulnerabilidades.
![04 - ZAP Varredura Ativa](../evidencias/etapa-5/04-zap-varredura-ativa.png)

#### 5. Painel Consolidado de Alertas Gerados

Painel de alertas do OWASP ZAP apresentando os achados identificados durante a sessão de verificação e suas respectivas classificações de risco.
![05 - ZAP Alertas Gerados](../evidencias/etapa-5/05-zap-alertas-gerados.png)

#### 6. Efeito Visível das Requisições de Ataque no Alvo

Registro do comportamento observado na interface do OWASP Juice Shop durante a execução das requisições automatizadas realizadas pelo scanner.
![06 - Efeito do Ataque no JuiceShop](../evidencias/etapa-5/06-efeito-do-ataque-no-juiceshop.png)

> ⚠️ **Nota sobre os alertas individuais:** As capturas específicas dos três alertas selecionados serão adicionadas a esta seção após a definição dos achados que serão analisados individualmente pela equipe, sendo um alerta de responsabilidade de Gabriela e dois de responsabilidade de Erik.

---

## 5.2 Tabela Geral de Achados e Alertas
*(PARTE DE INAURRARA; favor, apagar depois de preencher)*

---

## 5.3 Análise Detalhada do Alerta 1 (A01)
*(PARTE DA GABRIELA; favor, apagar depois de preencher)*

---

## 5.4 Análise Detalhada do Alerta 2 (A02)
*(PARTE DO ERIK; favor, apagar depois de preencher)*

---

## 5.5 Análise Detalhada do Alerta 3 (A03)
*(PARTE DO ERIK; favor, apagar depois de preencher)*

---

## 5.6 Análise de Falsos Positivos e Alertas Descartados
*(PARTE DE INAURRARA; favor, apagar depois de preencher)*

---

## 5.7 Considerações Finais da Etapa 5
*(PARTE DE INAURRARA; favor, apagar depois de preencher)*
