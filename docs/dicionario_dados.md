# Dicionário de Dados

## Fontes originais (abas do Excel usadas)
Tabela 4 (saldo UF x setor), Tabela 6 (série mensal por setor), Tabela 9 (salário nacional mensal), Tabela 10 (rotatividade por setor), Tabela 11 (rotatividade por UF/região).

## Dimensões

### Dim_Setor
Combina os setores das Tabelas 4, 6 e 10 (mesma taxonomia CNAE 2.0 agrupada), com `Text.Proper` mais `Text.Trim` pra eliminar duplicata por capitalização, e exclui a linha "Total".
| Coluna | Descrição |
|---|---|
| Setor | Nome do setor, padronizado em Proper Case |
| Nivel | 1, 2 ou 3: profundidade na hierarquia de grupamento (ver `decisoes_modelagem.md`) |
| EhAgregador | TRUE se a linha é soma de outras linhas da mesma coluna Setor (ex: "Indústria Geral", "Serviços") |
| Setor (Legenda) | Cópia idêntica de Setor, existe só pra contornar limitação do visual Small Multiples, que não aceita a mesma coluna em Múltiplos Pequenos e Legenda ao mesmo tempo |

### Dim_Setor_A / Dim_Setor_B
Cópias (via referência) de `Dim_Setor`, sem relacionamento com o resto do modelo. Usadas para permitir 2 slicers de setor independentes (um não filtra o outro), disponíveis pra uma visão de comparação de dois setores lado a lado. O cruzamento com a tabela fato é feito via `TREATAS` dentro das medidas, não por relacionamento.

### Dim_UF
Lista de UFs distintas vinda da Tabela 4.

### Dim_Data
Combina os meses de `stg_Tabela6` e `stg_Tabela9`, convertidos de texto ("Janeiro/2020") pra data real via tabela de tradução de nomes de mês em português.
| Coluna | Descrição |
|---|---|
| Mes | Texto original ("Julho/2026") |
| Data | Data real (primeiro dia do mês) |
| Ano | Ano extraído de Data |
| MesNumero | Número do mês (1 a 12) |

## Fatos

### Fato_Serie_Mensal (fonte: Tabela 6)
Formato longo: 1 linha por Setor x Mês x Métrica.
| Coluna | Descrição |
|---|---|
| Setor, Mes, Metrica, Valor | Ver estrutura original |
| EhTotal | TRUE só na linha "Total" |
| EhAgregador | TRUE nas linhas que somam outras linhas da mesma coluna (ver hierarquia) |
| Nivel | 0 (Total), 1, 2 ou 3 |
| Data | Data real do mês, usada pra relacionamento com Dim_Data e para funções de time intelligence (DATEADD) |

Métricas presentes: Estoque, Admissões, Desligamentos, Saldos, Variação Relativa (%).

### Fato_Saldo_UF_Setor (fonte: Tabela 4)
Granularidade: Setor x UF, foto única (jul/2026). Colunas: Setor, UF, Saldo.

### Fato_Rotatividade_Setor (fonte: Tabela 10)
Foto única (ago/2025 a jul/2026). Colunas: Setor, Admissoes, Desligamentos, EstoqueMedio, TaxaRotatividade. Atenção: TaxaRotatividade vem em escala percentual crua (ex: 59.84, não 0.5984), medidas que a exibem via `FORMAT(..., "%")` em DAX (não formatação nativa de campo) precisam dividir por 100 antes.

### Fato_Rotatividade_UF (fonte: Tabela 11)
Mesma estrutura de Fato_Rotatividade_Setor, mas por UF/Região.
| Coluna extra | Descrição |
|---|---|
| Nivel | "Brasil", "Região" ou "UF": classifica a granularidade de cada linha, já que a fonte mistura os 3 níveis geográficos na mesma coluna UF |

### Fato_Salario_Nacional (fonte: Tabela 9)
Série mensal, só nacional (sem quebra por setor). Colunas: Mes, SalarioMedioAdmissao, SalarioMedioDesligamento.

## Chaves de cruzamento
Nome do setor é texto padronizado (Proper Case), idêntico entre `Dim_Setor` e as 3 fatos que o usam, usado como chave direta via relacionamento, sem tabela de mapeamento adicional.
