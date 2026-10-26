# Ensaios de manejo da mandioca no Brasil

English version: [README.md](README.md)

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23649644.svg)](https://doi.org/10.5281/zenodo.23649644)

3.311 resultados de 77 ensaios sobre o cultivo da mandioca (*Manihot esculenta*) no Brasil, tirados das tabelas de 20 publicações de acesso aberto da Embrapa: ensaios de produtividade de cultivares e clones, adubação e calagem, época de plantio e espaçamento, controle de plantas daninhas, e pragas e doenças (viroses, bacteriose, podridões radiculares, mosca-branca, ácaros, mandarová). Cada linha é um número exatamente como foi impresso, com a página e a tabela de onde saiu, e aponta para o ensaio a que pertence.

A mandioca é alimento básico das famílias de baixa renda no Brasil e em todo o trópico, e boa parte dos dados sobre como cultivá-la bem está em boletins técnicos regionais, muitas vezes escaneados. Este dataset torna 20 deles pesquisáveis e comparáveis, de Roraima e do Pará ao Paraná.

Faz parte do [Paper Rerun](https://paper-rerun.github.io), na linha Open Tables. O trabalho foi feito por agentes de IA num computador doméstico e revisado por uma pessoa antes da publicação.

## O que tem

| | |
|---|---|
| Publicações | 20 (Alice e Infoteca-e, os repositórios abertos da Embrapa), 1982 a 2025 |
| Ensaios | 77 |
| Registros | 3.311 (produtividade de raízes, teor e produtividade de amido, matéria seca, notas de doenças e pragas, características da planta e outros) |

Arquivos:
- `data/cassava_trials.csv`: o dataset, um registro por valor impresso, com `doc_id` e `trial_id`. As colunas estão descritas em `method/schema.json`.
- `data/cassava_trials_context.csv`: uma linha por ensaio (local, safra, data de plantio e idade de colheita, cultivar, espaçamento, delineamento). Junte os dois arquivos por `doc_id` + `trial_id`; um campo de contexto vazio num registro significa que vale o valor do ensaio.
- `data/sources.csv`: as 20 publicações, com autores, link do registro e aviso de licença.
- `qa/decisions.csv`: cada divergência entre as duas transcrições independentes e como ela foi resolvida conferindo o PDF (2.921 decisões, com a página).
- `method/`: o schema, o script de comparação e o script de junção.

### Principais colunas

O arquivo está em UTF-8, com vírgula separando as colunas. Local, safra, plantio, idade de colheita, espaçamento e delineamento de cada ensaio estão em `data/cassava_trials_context.csv`.

- `doc_id`: identificador da publicação de origem. Liga o registro a `data/sources.csv` e é também o nome do PDF, sem a extensão.
- `trial_id`: o ensaio a que o valor pertence, dentro da publicação. Com `doc_id`, liga o registro à sua linha no arquivo de contexto.
- `page`: página do PDF contada a partir de 1 (a primeira página do arquivo é 1), e não o número impresso na página. O rótulo da tabela está em `table_or_figure`, como impresso ("Tabela 3", "Quadro 1"), ou `text` quando o número está no corpo do texto.
- `treatment`: rótulo da linha ou da coluna exatamente como impresso (cultivar, código, dose de adubo, herbicida, período, mês, "Testemunha", "Média"). Numa célula de fatorial, os níveis vêm unidos por " x ", na ordem da tabela.
- `biotic_target`: a doença, praga ou planta daninha que a variável mede, como `common_mosaic_virus` (mosaico-comum), `bacterial_blight` (bacteriose), `root_rot` (podridão radicular), `whitefly` (mosca-branca), `green_mite` (ácaro-verde), `hornworm` (mandarová) ou `weeds` (plantas daninhas). É `none` quando a variável não mede nenhuma delas (produtividade de raízes, amido, altura de planta), mesmo num ensaio sobre uma delas.
- `variable`: variável medida, exatamente como no cabeçalho ou na legenda, com o momento quando ele faz parte do cabeçalho ("PRT (t ha-1)", "Teor de amido (%)", "Controle (%) 28 DAA").
- `variable_standardized`: a mesma variável numa lista fechada. Alguns exemplos: `root_yield_t_ha` (produtividade de raízes frescas em t/ha), `starch_content_pct` (teor de amido nas raízes, %), `starch_yield` (produtividade de amido por área), `dry_matter_pct` (matéria seca das raízes, %), `shoot_yield` (massa da parte aérea), `pest_damage_score` (nota de dano de praga) e `disease_incidence_pct` (% de plantas, folhas ou raízes com doença, incluindo raízes podres). A lista completa está em `method/schema.json`.
- `value_as_printed`: a célula exatamente como impressa, sem letras, marcas de rodapé ou termo de dispersão ("26,5", "52.000", "MR"). Quando `value_kind` é `class` (classe de reação) ou `illegible` (célula que não dá para ler com segurança), `value` fica vazio.
- `value`: o mesmo número pronto para cálculo, com ponto decimal ("26,5" vira 26.5, "52.000" vira 52000). Em relação a `value_as_printed`, só muda a vírgula pelo ponto, sai o separador de milhar e sai um zero à esquerda. Nada foi calculado, convertido ou arredondado.
- `unit`: unidade como impressa ou informada na legenda ou no cabeçalho ("t/ha", "t ha-1", "Mg ha-1", "kg/pl", "%"). Vazia quando não há unidade impressa, como em notas e contagens.
- `statistic`: o que o número é: `mean` (média), `relative_to_check` (calculado pelos autores em relação à testemunha), `total`, `median`, `single_value` (uma parcela ou uma observação), `percentage_of_total` ou `other`. Quando o valor vem do rótulo da tabela inteira, `inherited_from_table` registra isso.
- `significance_letter`: letra(s) do teste de médias exatamente como impressas ("a", "AB", "a A", "bc"). Vazia quando não há. O teste e o significado das letras estão em `significance_test` e `letters_meaning`.

## Como foi feito

1. **Coleta.** Um agente buscou nos repositórios abertos da Embrapa publicações com tabelas de ensaios quantitativos sobre o tema e baixou 20, registrando cada candidata que não entrou, com o motivo.
2. **Duas transcrições independentes.** Dois agentes por publicação transcreveram o contexto dos ensaios, os rótulos que valem para uma tabela inteira (qual estatística, qual teste, o que as letras significam) e cada célula de tabela sobre o tema num schema fixo, sem ver o trabalho um do outro, copiando os números exatamente como impressos e prestando contas de cada célula de dados.
3. **Comparação e arbitragem.** Um script pareou as duas transcrições, ensaio por ensaio, tabela por tabela e registro por registro, e listou cada divergência e cada valor presente em só uma delas. Cada caso foi resolvido conferindo o PDF, com a página como evidência.

## Qual a precisão

- **Números:** somando as duas transcrições, 16 valores foram lidos errado (6 numa, 10 na outra), e todos foram pegos e corrigidos quando as duas divergiram. É por isso que cada documento é lido duas vezes.
- **Contexto e rótulos:** decidir os rótulos de tabela uma vez por tabela reduziu os erros de classificação de cerca de 150 a 300 por mil registros, na nossa rodada anterior, para 13 a 19 por mil aqui. Sozinha, uma transcrição estava totalmente correta em 68% dos registros, e a outra em 94%. Cada divergência foi resolvida conferindo o PDF, então o dataset não depende de nenhuma das duas sozinha.
- **Limites conhecidos:** valores mostrados só em gráficos, sem número impresso, não foram lidos. Alguns documentos mais antigos são escaneados, com tabelas impressas de lado, que foram lidas a partir da imagem da página.

## Licença e citação

- O dataset (os arquivos CSV, o schema e os scripts) é publicado sob a licença [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Por favor, cite as publicações originais listadas em `data/sources.csv` e também este dataset.
- As publicações não são redistribuídas. Os repositórios da Embrapa exibem um aviso CC BY-NC-ND 4.0; os artigos de periódico mantêm as licenças das revistas, e alguns PDFs trazem "todos os direitos reservados". Aqui são usados só os números, que são fatos, e citações curtas.

Erros ou ensaios que faltam: por favor, [abra uma issue](https://github.com/paper-rerun/cassava-trials-br/issues).

## Como este dataset foi feito

Este dataset foi montado por agentes de IA (Claude, da Anthropic) num computador doméstico e revisado por uma pessoa antes da publicação. Nenhum valor foi estimado ou digitado à mão: cada um foi transcrito do PDF original duas vezes, de forma independente, e cada divergência foi conferida na página. O método e os scripts estão em `method/`. Como funciona o projeto como um todo (em inglês): https://github.com/paper-rerun/paper-rerun/blob/main/METHOD.md

Contato: Filipe Tessaro, pelas issues deste repositório.
