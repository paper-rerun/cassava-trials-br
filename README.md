# Cassava management trials in Brazil

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23649644.svg)](https://doi.org/10.5281/zenodo.23649644)

3,311 trial results from 77 trials on growing cassava (*Manihot esculenta*, "mandioca") in Brazil, taken from the tables of 20 open-access Embrapa publications: cultivar and clone yield trials, fertilization and liming, planting date and spacing, weed control, and pests and diseases (viruses, bacterial blight, root rots, whitefly, mites, hornworm). Each row is one number exactly as printed, with the page and table it came from, and points to the trial it belongs to.

Cassava is a staple food for low-income households in Brazil and across the tropics, and much of the evidence on how to grow it well sits in regional technical bulletins, often scanned. This dataset makes 20 of them searchable and comparable, from Roraima and Pará to Paraná.

Part of [Paper Rerun](https://paper-rerun.github.io), in its Open Tables line. The work was done by AI agents on a home computer and reviewed by a human before publication.

## What is in it

| | |
|---|---|
| Publications | 20 (Alice and Infoteca-e, Embrapa's open repositories), 1982 to 2025 |
| Trials | 77 |
| Records | 3,311 (root yield, starch content and yield, dry matter, disease and pest scores, plant traits and others) |

Files:
- `data/cassava_trials.csv`: the dataset, one record per printed value, with `doc_id` and `trial_id`. Columns are described in `method/schema.json`.
- `data/cassava_trials_context.csv`: one row per trial (place, season, planting and harvest age, cultivar, spacing, design). Join on `doc_id` + `trial_id`; an empty context field in a record means the trial's value applies.
- `data/sources.csv`: the 20 publications, with authors, record link and licence notice.
- `qa/decisions.csv`: every disagreement between the two independent transcriptions and how it was settled against the PDF (2,921 decisions, with page).
- `method/`: the schema, the comparison script and the merge script.

## How it was made

1. **Collection.** An agent searched Embrapa's open repositories for publications with quantitative trial tables on this topic and downloaded 20, recording every candidate it did not take, with the reason.
2. **Two independent transcriptions.** Two agents per publication transcribed the trial context, the labels that apply to a whole table (which statistic, which test, what the letters mean) and every table cell on the topic into a fixed schema, without seeing each other's work, copying numbers exactly as printed and accounting for every data cell.
3. **Comparison and adjudication.** A script paired the two transcriptions, trial by trial, table by table and record by record, and listed every disagreement and every value present in only one of them. Each one was settled by checking the PDF, with the page as evidence.

## How accurate it is

- **Numbers:** across both transcriptions, 16 values were misread (6 in one, 10 in the other), all caught and fixed when the two disagreed. This is why each document is read twice.
- **Context and labels:** deciding table-wide labels once per table cut classification errors from about 150 to 300 per thousand records in our previous round to 13 to 19 per thousand here. One transcription alone was fully correct on 68% and 94% of records; every disagreement was settled against the PDF, so the dataset does not depend on either one alone.
- **Known limits:** values shown only in charts without printed numbers were not read; some older documents are scans with tables printed sideways, which were read from the rendered page.

## Licence and citation

- The dataset (the CSV files, schema and scripts) is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Please cite the original publications listed in `data/sources.csv` as well as this dataset.
- The publications are not redistributed. Embrapa's repositories show a CC BY-NC-ND 4.0 notice; journal articles keep their journals' licences and some PDFs print "all rights reserved". Only numbers, which are facts, and short citations are used here.

Errors or missing trials: please [open an issue](https://github.com/paper-rerun/cassava-trials-br/issues).

## How this dataset was made

This dataset was built by AI agents (Claude, by Anthropic) on a home computer and reviewed by a human before publication. No value was estimated or typed by hand: each one was transcribed from the source PDF twice, independently, and every disagreement was checked against the page. The method and scripts are in `method/`. How the wider project works: https://github.com/paper-rerun/paper-rerun/blob/main/METHOD.md

Contact: Filipe Tessaro, via issues on this repository.
