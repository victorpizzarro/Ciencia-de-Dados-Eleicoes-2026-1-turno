# Eleições 2026 (1º turno) — web scraping e métodos estatísticos

Atividade assistida de **Métodos Estatísticos em Ciência de Dados** · Análise de Sistemas Informatizados · FAETERJ.
Coleta dos resultados do 1º turno de 2026 (**Presidente, Senador, Deputado Federal e Deputado Estadual**) direto da
fonte primária (**TSE**, arquivos JSON públicos de `resultados.tse.jus.br`), limpeza com pandas e aplicação dos métodos apresentados em sala aula.

## Estrutura

```
├── data/
│   ├── raw/                 # JSONs originais do TSE (não versionado; o 01_ETL recria)
│   └── processed/           # CSVs dos 4 cargos (ETL) e tabelas de resultados da análise
├── notebooks/
│   ├── 01_ETL.ipynb         # coleta no TSE, limpeza e validação -> data/processed/*_1t.csv
│   ├── 02_Analise.ipynb     # mediana/IQR, moda, Spearman, z-score, PCA -> data/processed/ + reports/
│   └── 03_Visualizacao.ipynb# um gráfico por cargo + PCA -> reports/figures/
├── reports/
│   ├── figures/             # gráficos (PNG)
│   ├── paragrafo_limites_e_cuidados.md
│   └── leitura_loadings_pca.md
├── .gitignore
├── requirements.txt
└── README.md
```

## Como rodar

```bash
pip install -r requirements.txt
cd notebooks
jupyter lab        # abra e rode na ordem: 01_ETL -> 02_Analise -> 03_Visualizacao
```

O `01_ETL` baixa ~111 arquivos com pausa de 2 s (cerca de 4 min). Se `data/raw/` já existir, usa o cache e não baixa de novo.
Os notebooks 02 e 03 só dependem de `data/processed/`, que **está versionado** — dá para rodá-los sem refazer a coleta.

## Entregáveis da atividade (pág. 22) e onde estão

| Entregável | Local |
|---|---|
| Notebook (.ipynb) comentado | `notebooks/` (3 notebooks, já executados) |
| CSVs dos 4 cargos | `data/processed/presidente_1t.csv`, `senador_1t.csv`, `deputado_federal_1t.csv`, `deputado_estadual_1t.csv` |
| Um gráfico por cargo | `reports/figures/grafico_presidente.png`, `grafico_senador.png`, `grafico_deputado_federal.png`, `grafico_deputado_estadual.png` |
| Um PCA com leitura dos loadings | `reports/figures/pca_pc1_pc2.png`, `data/processed/pca_loadings.csv`, `reports/leitura_loadings_pca.md` |
| Parágrafo de 5 a 8 linhas sobre limites e cuidados | `reports/paragrafo_limites_e_cuidados.md` |

| Critério da rubrica (pontos) | Onde |
|---|---|
| Coleta dos 4 cargos (3) | `01_ETL`, seção 1 |
| Limpeza e validação (2) | `01_ETL`, seção 2 (soma dos votos = total do TSE em todas as 110 unidades; BR = soma das UFs + exterior) |
| Métodos estatísticos (3) | `02_Analise` |
| Interpretação e limites (2) | `02_Analise` (seções 4 a 6) |

## Arquivos em `data/processed/`

| Arquivo | Origem | Conteúdo |
|---|---|---|
| `*_1t.csv` (4) | 01_ETL | uma linha por cargo × UF × candidato (`cargo, uf, candidato, partido, votos, pct_validos, ...`) |
| `metadados_tse.csv` | 01_ETL | quando o TSE gerou cada arquivo, % de seções, totais e `totalizacao_final` |
| `log_coleta.csv` | 01_ETL | cada requisição feita (URL, status, horário) |
| `analise_*.csv` | 02_Analise | estatísticas por candidato, por UF, moda do partido, Spearman |
| `pca_*.csv` | 02_Analise | variância explicada, loadings e scores das UFs |

## Pontos de atenção

- **Rondônia (Deputado Federal e Estadual):** o arquivo do TSE veio sem totalização final (`tf = "n"`) e sem a situação dos
  candidatos; por isso o campo `resultado` está como "sem totalização final". Os **votos estão completos e conferem** com o total do TSE.
  A imprensa já divulgava os eleitos de RO; o motivo da pendência no arquivo não está confirmado. Os dados foram mantidos como o TSE os publicou.
- No **DF**, o cargo de deputado estadual é **Deputado Distrital** (`cargo_tse` informa).
- Votos **"Anulado sub judice"** ficam nos CSVs (`situacao_candidatura`), mas fora de totais, percentuais e estatísticas.
- Votos de **legenda** (deputados) não pertencem a candidatos e não estão nos CSVs.
- Dados coletados em **15/10/2026**; resultados podem mudar com o julgamento de candidaturas.

