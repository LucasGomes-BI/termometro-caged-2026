# Termômetro do Mercado de Trabalho Formal Brasileiro (Jul/2026)

Análise do CAGED (jul/2026) em Power BI, com transformação feita 100% em Power Query.

## Fonte
Arquivo original do MTE: `data/raw/3-tabelas_Julho_de_2026.xlsx`

## Estrutura

- `data/raw/` — arquivo original, sem alteração
- `powerbi/` — projeto Power BI no formato .pbip (Report + SemanticModel)
- `docs/` — dicionário de dados e decisões de modelagem
- `assets/screenshots/` — prints do relatório final

## Camadas do modelo

1. **Série mensal por setor** (Tabela 6) — evolução jan/2020 a jul/2026
2. **Saldo por UF e setor** (Tabela 4) — foto jul/2026
3. **Rotatividade x salário** (Tabelas 9, 10, 11) — cruzamento estático ago/25-jul/26

## Como abrir
Power BI Desktop > abrir `powerbi/*.pbip` (requer preview "Power BI Project save option" habilitado em Options > Preview features).
