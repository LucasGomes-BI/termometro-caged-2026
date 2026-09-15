# Termômetro do Mercado de Trabalho Formal Brasileiro (Jul/2026)

Análise do CAGED (jul/2026) em Power BI, com transformação 100% em Power Query e modelagem em estrela.

## Fonte
Arquivo original do MTE: `data/raw/3-tabelas_Julho_de_2026.xlsx`

## Estrutura

- `data/raw/` — arquivo original, sem alteração
- `powerbi/` — projeto Power BI no formato .pbip (Report + SemanticModel)
- `docs/` — dicionário de dados e decisões de modelagem
- `assets/screenshots/` — prints do relatório final

## Páginas do relatório

1. **Visão Geral** — KPIs nacionais (Saldo, Estoque, Admissões, Desligamentos, Variação interanual) com comparativo mês/ano anterior, série mensal de saldo com destaque visual pro período da pandemia (jan-abr/2020)
2. **Evolução por Setor** — small multiples com a evolução mensal do saldo por grande grupamento (Nível 1), eixo Y sincronizado entre painéis
3. **Saldo por Estado** — ranking de estados por saldo de vagas, filtrável por setor
4. **Rotatividade x Salário** — cruzamento entre taxa de rotatividade e saldo por setor (gráfico de dispersão), evolução do salário real nacional, e ranking de rotatividade
5. **Rotatividade por Região** — mapa de formas (Shape Map) colorido por taxa de rotatividade e ranking de estados

## Modelo de dados

Estrela clássica: 3 dimensões (`Dim_Setor`, `Dim_UF`, `Dim_Data`) + 5 fatos (`Fato_Serie_Mensal`, `Fato_Saldo_UF_Setor`, `Fato_Rotatividade_Setor`, `Fato_Rotatividade_UF`, `Fato_Salario_Nacional`), mais duas dimensões auxiliares desconectadas (`Dim_Setor_A`, `Dim_Setor_B`) usadas para comparação lado a lado de dois setores na página de Saldo por Estado.

Detalhes de granularidade, tratamento de hierarquia de setor e decisões de design de cada medida estão em `docs/dicionario_dados.md` e `docs/decisoes_modelagem.md`.

## Como abrir
Power BI Desktop > abrir `powerbi/*.pbip` (requer preview "Power BI Project save option" habilitado em Options > Preview features).
