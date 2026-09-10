# Dicionário de Dados

## Tabela 4 — Saldo por UF e Setor (jul/2026, foto única)
Cabeçalho real: linha 6. Dados: linha 7 em diante.
| Coluna | Descrição |
|---|---|
| Grupamento de Atividades Econômicas | Setor (linha) |
| Unidade da Federação | UF (colunas C:AC) |
| Valor | Saldo de vagas em jul/2026 |

## Tabela 6 — Evolução Mensal por Setor (jan/2020–jul/2026)
Cabeçalho de mês: linha 5 (mesclado, 4 ou 5 colunas por mês).
Cabeçalho de métrica: linha 6. Dados: linha 7 em diante.
| Métrica | Descrição |
|---|---|
| Estoque | Vínculos ativos no mês |
| Admissões | Contratações no mês |
| Desligamentos | Desligamentos no mês |
| Saldos | Admissões − Desligamentos |
| Variação Relativa (%) | Var. do estoque vs mesmo mês do ano anterior (ausente em jan/2020) |

Obs.: os dois últimos blocos de colunas ("Acumulado do Ano" e "Últimos 12 Meses") não são meses e devem ser tratados à parte ou descartados na Power Query.

## Tabela 9 — Salário Médio Real (nacional, mensal)
Cabeçalho: linha 5. Dados: linha 6 a 84.
Atenção: **não tem quebra por setor**, é só Brasil.
| Coluna | Descrição |
|---|---|
| Mês | Competência |
| Salário Médio Real de Admissão | Deflacionado pelo INPC |
| Salário Médio Real de Desligamento | Deflacionado pelo INPC |

## Tabela 10 — Taxa de Rotatividade por Setor (ago/25–jul/26, foto única)
Cabeçalho: linha 6. Dados: linha 7 em diante.
| Coluna | Descrição |
|---|---|
| Admissões, Desligamentos, Estoque Médio, Taxa de Rotatividade | Acumulado 12 meses |

## Tabela 11 — Taxa de Rotatividade por Região/UF (ago/25–jul/26, foto única)
Mesma estrutura da Tabela 10, granularidade geográfica em vez de setorial.

## Chave de cruzamento entre tabelas
Nome do setor é **texto livre**, igual entre Tabelas 4, 6 e 10 (mesma taxonomia CNAE 2.0 agrupada). Usar como chave direta, sem tabela de mapeamento.
