# Ranking ATP: limpeza e modelação de dados em MySQL

Projeto da unidade curricular **Gestão de Big Data** (Mestrado em Business Intelligence, ISCTE – Instituto Universitário de Lisboa), ano letivo 2025/2026.

A partir de um dataset de jogos de ténis profissional, o projeto percorre o processo completo de preparação de dados em SQL: **importação, limpeza e padronização, remoção de duplicados, construção de um modelo relacional e queries analíticas**. Cada decisão de limpeza está documentada no relatório.

## Dataset

"ATP Tennis Professionals": todos os jogos individuais de 10 361 jogadores (sexo masculino) que estiveram no top 500 do ranking, entre 28/03/1973 e 14/02/2022. Tem 14 variáveis: `PlayerName`, `Born`, `Height`, `Hand`, `Tournament`, `Location`, `Date`, `Ground`, `Prize`, `WL`, `Score`, `Round`, `OpponentRank` e `Opponent`.

Foi ainda adicionada informação sobre países (códigos ISO 3166-1) e a sua divisão em continentes.

> O ficheiro `atpplayers.csv` foi disponibilizado pelo docente e **não está incluído** neste repositório.

## Limpeza e padronização

| Coluna | Problema | Tratamento |
|--------|----------|------------|
| `Born` | Vários formatos (cidade, estado e país; só cidade; só país) | Separada em `Born_City` e `Born_Country`, com correção de valores trocados e países em falta |
| `Height` | Valores irreais (3 cm, 244 cm, etc.) | Valores fora do intervalo 147–211 cm passam a `NULL` |
| `Hand` | Mão dominante e estilo de backhand na mesma coluna | Separada em `Hand_Dominante` e `Backhand_Style` |
| `Tournament` | Nomes diferentes para o mesmo torneio (ex.: *French Championships* e *Roland Garros*), sufixos de semana, erros de HTML | Padronização dos nomes (Grand Slams, ATP Finals, Masters 1000, etc.) |
| `Location` | Cidades, estados e países misturados; valores `TBA`/`TBD`/`TBC`; locais ambíguos | Mantido só o país, com correção manual dos casos duvidosos |
| `Date` | Data de início e de fim na mesma coluna | Separada em `StartDate` e `EndDate` |
| `Prize` | Valor e símbolo da moeda juntos | Separada em `PrizeValue` e `PrizeCurrency` (EUR, USD, AUD, GBP) |
| `Score`, `WL`, `Ground`, `OpponentRank`, `Opponent` | Strings vazias, `null` em texto e `-` | Convertidos para `NULL`; marcadores `W/O`, `RET` e `DEF` tratados |
| `Round` | Nomes inconsistentes das fases | Normalizados (ex.: `Round of 8`, `Round of 4`, `Round of 2`) |

**Duplicados:** foram identificadas e removidas 2 830 linhas duplicadas (impacto de cerca de 0,2% no dataset).

**Erro estimado:** os valores `NULL` resultantes da limpeza representam cerca de 5% dos dados, o que deve ser tido em conta ao interpretar as estatísticas.

## Modelo relacional

5 tabelas, com chaves primárias e estrangeiras (`ON UPDATE CASCADE` e `ON DELETE RESTRICT` ou `SET NULL`) e índices nas colunas mais consultadas.

| Tabela | Descrição |
|--------|-----------|
| `continent` | Continentes e respetiva área |
| `country` | Países (código ISO de 2 letras) e continente |
| `tournament` | Torneios, datas, piso e país |
| `player` | Jogadores, altura, mão dominante, estilo de backhand, cidade e país de nascimento |
| `match_` | Jogos: resultado, fase, adversário, vitória/derrota e ranking do adversário |

## Queries analíticas

1. **Jogadores, torneios e jogos por país.** A Austrália lidera em torneios e jogos, e a Argentina em jogadores.
2. **Top 10 de jogadores por percentagem de vitórias** (mínimo de 10 jogos). Bjorn Borg lidera com cerca de 80%.
3. **Top 10 de jogadores canhotos nos Grand Slams.** Rafael Nadal lidera com cerca de 88%.
4. **Top 5 de jogadores com mais vitórias em piso duro.** Roger Federer lidera com 819 vitórias.

## Ferramentas

MySQL · phpMyAdmin · XAMPP
