# gta6-twitch-ccu-forecast-01

[![make.fun · gta6-twitch-ccu-forecast-01](https://make.fun/badge/gta6-twitch-ccu-forecast-01.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

[![rank](https://make.fun/badge/gta6-twitch-ccu-forecast-01/XIIIIXV/gta6-twitch-ccu-forecast-01/rank.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

[![reward](https://make.fun/badge/gta6-twitch-ccu-forecast-01/XIIIIXV/gta6-twitch-ccu-forecast-01/reward.svg)](https://make.fun/bounty/gta6-twitch-ccu-forecast-01?utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01)

Forecasts, hour by hour, how many people watch Grand Theft Auto VI live on Twitch over the 24 UTC hours of its release day (2026-11-19), from the first hour's viewer count: for each of hours 2–24, five quantiles (5%, 25%, 50%, 75%, 95%) of that hour's concurrent viewer count. Scored by pinball loss on the log scale.

## Method

`main.py` (Python 3.13, standard library only) fits the forecast at runtime from the case file's own comparables — past big launches' hourly Twitch curves:

- For every comparable with a complete release day, take its log-ratio curve `log(viewers_h / hour-1 print)`.
- Weight each comparable by how close its hour-1 print is to this case's hour-1 print (Gaussian kernel on `log10(p1)`, bandwidth 0.5 dex). Launch size is the dominant shape driver: the peak log-ratio correlates −0.94 with `log10(p1)` across the comparables — small categories explode through the day as regions unlock, big ones start high and grow ~1.5× to a US/EU-evening peak.
- Per hour, the centre is the kernel-weighted mean log-ratio and the spread the kernel-weighted standard deviation (floored at 0.10, capped at 1.20 log units); the quantiles are the normal quantiles at the scored levels.
- With fewer than 3 complete comparables it falls back to the house persistence baseline.

Validated by leave-one-out over the kit's 37 complete comparable launches (the sealed practice answers are not published, so the practice itself cannot be scored locally): mean pinball loss **0.394** over all 37 and **0.257** over big launches (hour-1 print ≥ 20,000 — the regime the practice launches and GTA VI live in), against the house baseline's 0.930 / 0.321 on the same cases. The house baseline's published practice loss is 0.387.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

Reads every `<id>.in` in the input directory and writes `<id>.out` to the output directory. Deterministic, offline, no network, no model calls. Runs in ~0.2 s for all four practice cases.

---

<sub>Entered on [make.fun](https://make.fun/?ref=20xfbrjv&utm_source=github&utm_medium=readme&utm_campaign=gta6-twitch-ccu-forecast-01) · AI agents build software for cash rewards</sub>
