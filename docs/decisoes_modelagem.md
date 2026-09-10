# Decisões de Modelagem

## Padrão de queries no Power Query
- Uma query de staging por tabela (`stg_Tabela4`, `stg_Tabela6`, etc.), desabilitada de carregar no modelo.
- Queries finais (`Fato_Saldo_UF_Setor`, `Fato_Serie_Mensal`, `Fato_Rotatividade_Setor`, `Fato_Rotatividade_UF`, `Fato_Salario_Nacional`) carregam pro modelo.
- Agrupar em pastas de exibição (Query Groups): `Staging` e `Fatos`.

## Tabela 6 — unpivot
Estrutura wide (mês x métrica em colunas). Tratamento sugerido:
1. Remover linhas de rodapé (fonte, notas).
2. Promover cabeçalhos combinando linha 5 (mês) + linha 6 (métrica) antes de carregar (ou via `Table.TransformColumnNames` com offset).
3. Unpivot das colunas de métrica, mantendo Setor como chave.
4. Separar coluna "Atributo" combinada em duas: Mês e Métrica.
5. Excluir blocos "Acumulado do Ano" e "Últimos 12 Meses" (não são mês real).
6. Converter "Mês" de texto (`Janeiro/2020`) pra tipo Date — precisa de tabela de tradução PT-BR de mês, ou `Date.MonthName` reverso.

## Tabela 4 — unpivot
Estrutura wide (UF em colunas). Unpivot direto, sem tratamento de data.

## Dimensão Setor
Considerar criar uma dimensão separada (`Dim_Setor`) com hierarquia:
Grupamento macro (Indústria, Serviços etc.) > Subsetor.
Hoje a tabela já vem com essa hierarquia implícita na indentação dos nomes — decidir se replica via coluna calculada ou mantém achatado.

## Relacionamentos
- `Dim_Setor[Setor]` 1:N com todas as fatos por setor.
- `Dim_Data[Data]` 1:N com `Fato_Serie_Mensal` e `Fato_Salario_Nacional`.
- `Dim_UF` 1:N com `Fato_Saldo_UF_Setor` e `Fato_Rotatividade_UF`.
- Tabelas de foto única (Rotatividade) não têm relação com Dim_Data (são um único período, ago/25-jul/26).

## Medidas DAX candidatas
- Saldo acumulado 12 meses
- Variação % ano contra ano
- Rotatividade média ponderada por setor selecionado
- Salário real (admissão vs desligamento), gap %
