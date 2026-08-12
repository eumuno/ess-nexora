# 5 — Verificação de Vulnerabilidades

## 5.1 Objetivo e escopo

Esta etapa demonstra a verificação dinâmica de segurança (DAST) em ambiente de
laboratório controlado. A Nexora é documental e não possui aplicação executável
versionada; por isso, os resultados são do OWASP Juice Shop e não constituem
falhas comprovadas da Nexora. A relação com a Nexora é feita apenas como análise
de impacto e de controles que seriam aplicáveis à plataforma.

## 5.2 Metodologia

1. Executar o OWASP Juice Shop localmente, isolado de sistemas externos.
2. Configurar o OWASP ZAP para o alvo autorizado `http://localhost:3000`.
3. Realizar o mapeamento e a varredura ativa dentro do escopo definido.
4. Guardar capturas e relatórios na pasta `evidencias/etapa-5/`.
5. Analisar cada alerta com evidência, impacto, relação CWE/OWASP, correção e
   possível falso positivo antes de classificá-lo como confirmado ou descartado.

## 5.3 Ambiente resumido

| Item | Informação |
| :--- | :--- |
| Aplicação-alvo | OWASP Juice Shop v20.1.1 |
| Execução | Docker Desktop v4.85.0 em Windows 11 |
| Endereço e escopo | `http://localhost:3000`, somente ambiente local autorizado |
| Ferramenta DAST | OWASP ZAP v2.17.0 |
| Data documentada | 09 de agosto de 2026 |
| Responsável pela execução | Bruna Rosa Ferreira |

## 5.4 Resultado e encaminhamento

As imagens da execução e o relatório detalhado estão em
[`evidencias/etapa-5/`](../evidencias/etapa-5/). O relatório de consolidação
centraliza a tabela de achados, as análises dos integrantes, falsos positivos,
alertas descartados e limitações:

- [Relatório detalhado da verificação](../evidencias/etapa-5/relatorio-da-verificacao.md)

O Alerta 1 é analisado pela Gabriela; os Alertas 2 e 3, caso existam, são
analisados pelo Erik. A consolidação final depende dessas análises e da revisão
dos possíveis falsos positivos.
