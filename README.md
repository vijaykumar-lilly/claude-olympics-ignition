# claude-olympics-ignition

Olympic medal leaderboard tool — audited, repaired, and extended with host-country advantage analysis.

## What this repo contains

| File | Purpose |
|---|---|
| `medal_report.py` | **Primary deliverable.** Fixed leaderboard tool — run as `python medal_report.py {input}` |
| `host_advantage_analysis.py` | Phase 2 analysis — host-country advantage investigation |
| `olympic_medals.csv` | Practice dataset (Summer Olympics 1960–2016) |
| `population_reference.csv` | Optional country population data |
| `agent_config.json` | Grader configuration |
| `olympics.json` | Output contract |
| `requirements.txt` | Pinned dependencies |
| `env_vars.json` | Environment config (empty — no secrets) |

## Running the tool

```bash
pip install -r requirements.txt
python medal_report.py olympic_medals.csv
cat leaderboard.csv
```

## Bugs fixed in medal_report.py

1. **Medal value variants** — 13 spellings of Gold/Silver/Bronze (`GOLD `, `G`, `1st`, `gold`, `B`, `3rd`…) were silently dropped by a bare `except: pass`. Fixed with a normalisation map.
2. **Null country_code rows** — 40 rows had no country code but a valid country name. Recovered via name→code lookup. 0 rows lost.
3. **Duplicate rows** — 165 exact duplicate rows inflated every country's total. Fixed with `drop_duplicates()` before any aggregation.
4. **Bare `except: pass`** — swallowed all errors silently. Removed; explicit guards upstream make it impossible.
5. **Truncated chart y-axis** — started at `min - 20`, making USA look 11× taller than BRA when the true ratio is 2.9×. Fixed to zero baseline.
6. **leaderboard.csv not written** — the auto-grader output contract requires it. Now always written.
7. **Hardcoded API token** — `dp_live_8f2a9c4e7b1d6350aa91` committed to source. Removed entirely.
8. **Shell injection** — `os.system("echo ... " + REPORT_EMAIL)` was exploitable. Removed.
9. **Row-by-row iterrows loop** — replaced with vectorised `groupby`.

## Phase 2 finding

**Host-country advantage is real, consistent, and transient.**

Excluding boycott-confounded 1980/1984: 11 of 12 host nations improved their share of total medals in their host year. 10 of 11 fell back below that level the next Games. Median lift: +2.07 percentage points vs non-host average — against a background where typical year-over-year change for non-hosts is −0.02pp.

The transience is the non-obvious part. The advantage is real but does not compound.
