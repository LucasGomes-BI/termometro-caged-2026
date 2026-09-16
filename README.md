# Termômetro do Mercado de Trabalho Formal Brasileiro (Jul/2026)

Análise do CAGED (jul/2026) em Power BI, com transformação 100% em Power Query e modelagem em estrela.

## Fonte
Arquivo original do MTE: `data/raw/3-tabelas_Julho_de_2026.xlsx`

## Estrutura

- `data/raw/`: arquivo original do MTE, sem alteração
- `data/br-states.json`: TopoJSON customizado dos estados brasileiros, usado no visual Mapa de Formas (Shape Map) da página Rotatividade por Região
- `powerbi/`: projeto Power BI no formato .pbip (Report + SemanticModel)
- `docs/`: dicionário de dados e decisões de modelagem
- `assets/screenshots/`: prints do relatório final

## Páginas do relatório

1. **Visão Geral**: 5 cartões de KPI nacional (Saldo, Estoque, Admissões, Desligamentos, Variação interanual), cada um com comparativo textual vs. mês anterior e vs. ano anterior. Gráfico de linha do saldo mensal nacional, jan/2020 a jul/2026, com destaque visual (faixa vermelha) no período da pandemia (jan a abr/2020). Card de texto explicando o que é o CAGED.
2. **Evolução por Setor**: small multiples com a evolução mensal do saldo por grande grupamento (Nível 1: Agricultura, Comércio, Construção, Indústria Geral, Serviços), eixo Y sincronizado entre painéis, cor própria por setor. Slicer de ano. Card de resumo automático (maior queda na pandemia e setor líder no mês atual).
3. **Saldo por Estado**: ranking de estados por saldo de vagas em ordem decrescente, filtrável por setor via slicer. Card de resumo automático (estado líder, % do saldo nacional, quantos estados tiveram saldo negativo).
4. **Rotatividade x Salário**: gráfico de dispersão cruzando Taxa de Rotatividade (eixo X) e Saldo do setor (eixo Y), tamanho da bolha proporcional ao Estoque Médio. Gráfico de linha do salário real médio nacional (admissão x desligamento), mensal. Tabela de ranking de rotatividade por setor. Card de resumo automático.
5. **Rotatividade por Região**: mapa de formas (Shape Map, TopoJSON customizado) colorido por taxa de rotatividade por UF, gradiente teal a âmbar. Tabela de ranking de estados, com linha de Total como referência nacional. Nota metodológica sobre a natureza de foto única do indicador.

## Modelo de dados

Estrela clássica: 3 dimensões (`Dim_Setor`, `Dim_UF`, `Dim_Data`) mais 5 fatos (`Fato_Serie_Mensal`, `Fato_Saldo_UF_Setor`, `Fato_Rotatividade_Setor`, `Fato_Rotatividade_UF`, `Fato_Salario_Nacional`), com duas dimensões auxiliares desconectadas (`Dim_Setor_A`, `Dim_Setor_B`) disponíveis no modelo para comparação de dois setores lado a lado.

Detalhes de granularidade, tratamento de hierarquia de setor e decisões de design de cada medida estão em `docs/dicionario_dados.md` e `docs/decisoes_modelagem.md`.

## Como abrir
Duas opções dentro de `powerbi/`, ambas com o mesmo conteúdo:

- **`Termometro_CAGED_Jul2026.pbip`** (recomendado): abre com o código-fonte completo (Power Query e DAX) versionado em texto dentro das pastas `.Report` e `.SemanticModel`. Requer baixar o repositório inteiro (não arquivos soltos) e ter o preview "Power BI Project save option" habilitado em Options > Preview features no Power BI Desktop.
- **`Termometro_CAGED_Jul2026.pbix`**: arquivo único tradicional, basta baixar e abrir direto, sem configuração prévia.

Em ambos os casos, o caminho do arquivo Excel de origem está fixo na máquina onde o projeto foi criado. Se abrir em outro computador, corrija o caminho em Editor Power Query > Configurações da Fonte de Dados antes de atualizar os dados.
