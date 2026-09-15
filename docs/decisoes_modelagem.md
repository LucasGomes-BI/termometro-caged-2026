# Decisões de Modelagem

## Hierarquia de setor (Tabela 6)
A fonte mistura 3 níveis de detalhe na mesma coluna Setor, identificados pela indentação original das células no Excel:

```
Total (Nivel 0)
Agricultura, Construção, Comércio, Não Identificado (Nivel 1, folhas)
Indústria Geral (Nivel 1, agregador)
    Indústrias Extrativas, de Transformação, Eletricidade e Gás, Água/Esgoto (Nivel 2, folhas)
Serviços (Nivel 1, agregador)
    Transporte, Alojamento, Serviços Domésticos (Nivel 2, folhas)
    Informação/Comunicação e Atividades Financeiras... (Nivel 2, agregador)
        5 subsetores (Nivel 3, folhas)
    Administração Pública/Educação/Saúde... (Nivel 2, agregador)
        3 subsetores (Nivel 3, folhas)
    Outros Serviços (Nivel 2, agregador)
        3 subsetores (Nivel 3, folhas)
```

Somar todas as linhas sem distinguir agregador de folha conta cada valor 2 ou 3 vezes (pai mais filho). As colunas `EhTotal` e `EhAgregador`, criadas em `stg_Tabela6` e propagadas para `Dim_Setor` e `Fato_Serie_Mensal`, resolvem isso: toda medida aditiva (Saldo, Estoque, Admissões, Desligamentos) filtra `EhTotal = FALSE, EhAgregador = FALSE` para somar só folhas, e usa `EhTotal = TRUE` quando o objetivo é o valor nacional pronto direto da fonte.

## Padrão de nomenclatura das medidas
- Sem sufixo (`Saldo`, `Estoque`...): soma as folhas, respeita qualquer filtro de setor do contexto (usada em visuais quebrados por setor).
- Sufixo "Nacional" (`Saldo Nacional`, `Estoque Nacional`...): trava em `EhTotal = TRUE`, ignora seleção de setor, usada nos cartões da Visão Geral.
- Sufixo "Contexto": string de texto pronta com comparação MoM/YoY, construída com `VAR` dentro da própria medida, sem medidas intermediárias separadas para "mês anterior", "variação" etc, reduzindo a quantidade de medidas no modelo.

## Comparação de 2 setores lado a lado
O modelo mantém 2 dimensões desconectadas (`Dim_Setor_A`, `Dim_Setor_B`), cada uma alimentando um slicer independente, disponíveis pra uma visão de comparação. O cruzamento com a fato é feito via `TREATAS`, não por relacionamento físico:
```dax
Saldo Setor A =
CALCULATE(
    SUM(Fato_Saldo_UF_Setor[Saldo]),
    TREATAS(VALUES(Dim_Setor_A[Setor]), Fato_Saldo_UF_Setor[Setor])
)
```
Exclusão mútua entre os 2 slicers (o setor escolhido em A some da lista de B e vice-versa) é feita comparando `SELECTEDVALUE` das duas tabelas desconectadas, funcionando mesmo sem relacionamento entre elas porque a avaliação roda no nível do filtro do visual do slicer, não no dado.

A página final "Saldo por Estado" usa a versão mais simples desse modelo: 1 slicer único de `Dim_Setor[Setor]` filtrando um ranking de barras horizontal por `Dim_UF`, com medida de resumo textual (`Resumo Estados`) identificando o estado líder, sua participação percentual no saldo nacional do setor selecionado, e a contagem de estados com saldo negativo.

## Rotatividade: taxa pronta vs taxa recalculada
`TaxaRotatividade` na fonte é uma métrica por linha, não é aditiva (não pode somar ou tirar média direto entre setores diferentes). Duas medidas cobrem os 2 cenários:
- `Taxa Rotatividade (linha única)`: usa `SELECTEDVALUE`, correta só quando o contexto já restringe a 1 setor.
- `Taxa Rotatividade Recalculada`: reconstrói a taxa a partir de (Admissões + Desligamentos) / 2 dividido pelo Estoque Médio somados, correta mesmo com múltiplos setores no contexto (ex: cartão com seleção múltipla).

Mesmo padrão replicado para UF em `Taxa Rotatividade UF Recalculada`, com filtro adicional `Nivel = "UF"` pra não misturar com as linhas de "Brasil"/"Região" que `Fato_Rotatividade_UF` também contém.

## Small Multiples com cor por categoria
O visual nativo de Small Multiples não permite cor condicional por painel quando a mesma coluna está no campo "Múltiplos Pequenos" e não está também na "Legenda", o motor de consulta não expõe contexto de filtro individual pra cada painel nesse caso. A coluna `Dim_Setor[Setor (Legenda)]` (cópia idêntica de `Setor`) existe só pra contornar isso: colocando a original em Múltiplos Pequenos e a cópia em Legenda, cada painel passa a ter contexto de série próprio, permitindo cor por `SWITCH(SELECTEDVALUE(...))` (medida `Cor Dinamica Setor`).

Tentar usar a mesma coluna física nos dois campos gera o erro `InvalidOrMalformedDataShapeBinding_RepeatedIndicesProjectionsOrGroupBy`.

## Medidas de resumo em texto (VAR mais RETURN concatenado)
Todas as páginas têm 1 medida de resumo textual (`Resumo Estados`, `Resumo Rotatividade Salario`, `Resumo Evolucao Setor`, `Resumo Comparativo A x B`), construídas com tabelas virtuais via `ADDCOLUMNS`, `FILTER`, `TOPN` dentro de `VAR`, retornando uma frase montada com `FORMAT` e concatenação de texto. Evita criar múltiplas medidas auxiliares (líder, segundo colocado, contagem) que só seriam usadas uma vez.
