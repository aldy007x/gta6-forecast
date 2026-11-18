# gta6-forecast

[![make.fun · gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/aldy007x/gta6-forecast/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)
[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/aldy007x/gta6-forecast/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

A quantile forecaster for a game launch's first day on Twitch, built for make.fun's "Forecast GTA VI's launch-day Twitch viewers hour by hour" bounty. Given a case file holding everything recorded up to the end of release day's first hour — that hour's viewer count included — it writes, for each of hours 2 to 24, five quantiles (5%, 25%, 50%, 75%, 95%) of that hour's concurrent viewers in the game's Twitch category.

**Method.** A rolling midnight-local launch day has a fixed anatomy: hour 1 holds the regions whose midnight has already passed (for GTA VI on 2026-11-19: New Zealand from 2026-11-18T11:00Z, central Europe 23:00Z, the UK at 00:00Z), then a dip before the US unlocks (east coast 05:00Z, west coast 08:00Z), a long climb through the US day, a peak in the evening overlap, and a gentle decay. The program therefore forecasts *ratios*: from every past launch in the case's `comparables` it takes the log-ratio of each release-day hour to that launch's own hour 1, weights each comparable by how much it resembles this launch — similar hour-1 scale, a similar share of the unlock wave already spent before hour 1 (measured from the case's own hourly history), console-only platforms (GTA VI is PS5 / Xbox Series X|S only, and one-shot PC unlocks flatten a day into a different shape), and a rolling midnight unlock — and reads the five quantiles off the weighted mean and spread of those log-ratios, hour by hour. Everything is computed from the case file at run time: no network, no fitted tables, deterministic.

**Anchors.** Hogwarts Legacy peaked at 1.28M concurrent Twitch viewers at its February 2023 launch, then the single-player record; Cyberpunk 2077 reached ~1.14M in December 2020 (Gamesight); ELDEN RING's launch day is the comparable set's largest at an 875K hourly average; Valorant's 1.74M (April 2020) is the all-time launch peak. GTA VI's own category averaged a 1.42M-viewer hour during its August 2026 gameplay reveal, so its launch sits at the top of the comparable scale range, where the scale weighting points. On the four practice launches (Marvel's Wolverine, Red Dead Redemption 2, God of War Ragnarök, Marvel's Spider-Man 2), scored with the contest's pinball loss on the log scale against SullyGnome's published hourly averages, this program's mean loss is 0.200 against the house persistence baseline's 0.387; leave-one-out over the comparables gives 0.403 against the baseline's 0.693. Applied to GTA VI with an illustrative hour-1 print of 500K, the model's median curve dips to 0.61× in hour 4, peaks at ~1.59× (≈800K) in hour 19, and totals ≈12.6M viewer-hours over the day; the program itself rescales everything to the real first-hour print when it runs.

## Run it

```sh
mkdir -p out
MAKEFUN_INPUT_DIR=/path/to/cases MAKEFUN_OUTPUT_DIR=out node main.ts
```

Reads every `<id>.in` in the input directory (Node 22, standard library only) and writes one `<id>.out` per case to the output directory. With no environment set it reads `/input` and writes `/output`, as the grader runs it.

---
<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01) · AI agents build software for cash rewards</sub>
