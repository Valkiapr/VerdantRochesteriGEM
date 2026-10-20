# VerdantRochesteriGEM
Farmer-friendly estimates of when fall armyworm caterpillars are most likely eating corn leaves, based on local weather and planting date. Available in English and Spanish.

This tool estimates the dates when fall armyworm caterpillars are most likely feeding on corn leaves rather than the ear. Enter your city and planting date, and it shows when corn reaches each growth stage and when each caterpillar wave is likely to feed on leaves. It is a research prototype from the Rochester iGEM 2026 VERDANT team and has not been tested in real fields, so use it alongside your own understanding.


# VIPER — Vigilant Insect Pest Emergence Radar

Team VERDANT, Rochester iGEM 2026

VIPER predicts when fall armyworm (*Spodoptera frugiperda*) caterpillars are likely to be feeding on corn **leaves** rather than the **ear**, so VERDANT's engineered biocontrol circuit (MeSA → SABP2 → shikimate-pathway amplification → Vip3A, with a theophylline-gated kill switch) has a real-world window for when it should be active. Enter a field location and a planting date, and VIPER returns:

- predicted corn growth-stage dates (VE, V6, VT, R1 — emergence, six-leaf/whorl, tasseling, silking),
- predicted fall armyworm leaf-feeding windows (Wave 1, Wave 2, ...), each flagged if it overlaps silking (meaning ear-feeding is also possible that wave),
- a plain-language headline summarizing today's risk status and the next predicted window, and
- a label stating whether the result used real live weather, Open-Meteo's historical archive, or demo/sample data.

It exists in two forms that share the same math: a dependency-free static HTML page (`VIPER.html`, formerly `VERDANT_MAYBE_FINAL_updated3.html`) meant for growers and judges, and a Python/Streamlit prototype (`app.py`) used during development. If you change a constant in one, change it in the other — see the comment at the top of each file.

## Quick start — static HTML version (recommended)

No install needed.

1. Clone or download this repository.
2. Open `VIPER.html` directly in any modern browser (Chrome, Edge, Safari, or Firefox).
3. Pick "Use my location" for real weather (needs internet) or "Try sample weather" for a fully offline demo.
4. Enter a planting date and, if known, the date you first saw signs of fall armyworm. Submit to see your predictions.

That's it — there is no server, build step, or hosted version. The team's PI asked that this stay as source code in GitHub rather than a live website, so every copy a grower or judge runs is their own.

## Quick start — Python / Streamlit prototype

Used for development and cross-checking against the static version; not required to use the tool.

```bash
pip install -r requirements.txt
streamlit run app.py
```

This opens a browser tab at `http://localhost:8501` with the same inputs and outputs as the static version.

Requirements: Python 3.9+, and the packages in `requirements.txt` (`streamlit`, `pandas`, `requests`, `matplotlib`). None of this is needed for the static HTML version.

## How it works

1. **Weather.** For a past planting date, real daily highs/lows come from Open-Meteo's historical archive API; the next 16 days always come from its forecast API. If no location is given, or the live lookup fails, the tool falls back to a smooth synthetic temperature curve (demo mode) so there's always something to show.
2. **Corn growth-stage model.** A standard growing-degree-day (GDD) calculation, base 10°C / ceiling 30°C, against published corn-stage thresholds (VE, V6, VT, R1), from U.S. extension references (Bayer Crop Science; University of Minnesota Extension).
3. **Fall armyworm generation-timing model.** A degree-day model (base 13.51°C; 171.5 DD eggs + 193.8 DD larvae + 124.7 DD pupae per generation) from Malekera et al. (2022). A 45/55 split estimates how much of the larval stage is spent on leaves/whorl (early instars) versus possibly on the ear (late instars) — this split is a placeholder; no published degree-day-specific early/late split exists yet (see Limitations).
4. **Risk-window engine.** Combines 2 and 3 into a calendar of leaf-feeding windows, flagging any window that runs past the corn's own silking date.

All of the biological reasoning behind each constant is commented directly above it in the code — see `VIPER.html`'s `<script>` section (and the equivalent in `app.py`).

## Limitations (also shown in the tool itself)

- Corn-stage thresholds use generic numbers, not tuned to a specific hybrid's maturity rating.
- The 45/55 early/late-instar leaf-vs-ear split is a placeholder, not a published value — it's the single biggest thing the model still needs.
- If no first-sighting date is entered, the tool guesses 30 days after planting; treat that as a rough starting point.
- Predictions more than 2–3 weeks out depend on weather *forecasts*, so they get less certain the further ahead they go.
- One fixed set of temperature thresholds is used everywhere; outside the temperate U.S. Corn Belt (where fall armyworm's biology differs — see Garcia et al. 2018; Early et al. 2018; Adan et al. 2024) treat the dates as rough estimates, not exact forecasts.
- Software validation and wet-lab validation are two separate things right now. VIPER's predicted windows have been checked against independent public pest-activity records (Cornell's NY Sweet Corn Trap Network — see the "Software" write-up, section 4.2), but not yet against bench data on when VERDANT's actual circuit turns on. That connection is still open.

## Repository contents

- `VIPER.html` — the static, dependency-free version. Open it directly in a browser.
- `app.py` — the Python/Streamlit prototype, same math as `VIPER.html`.
- `requirements.txt` — Python dependencies for `app.py` only.
- `README.md` — this file.

## Extending VIPER for a different pest or plant

The risk-window engine is deliberately generic. A future team modeling a different insect/plant pair (for example corn earworm, European corn borer, or soybean looper on a different crop, or a completely different plant-pest pair such as diamondback moth on brassicas) can reuse it by swapping in that pest's own degree-day parameters, developmental thresholds, and whatever mechanism their own circuit uses for detection — the same calendar, risk-window, and flagging logic carries over unchanged. See "Usefulness to other projects" in the Software write-up for more on this.

## Citations

The biological constants and regional comparisons in this tool come from:

- Cornell Integrated Pest Management, Cornell University College of Agriculture and Life Sciences. (n.d.). *Fall armyworm (Spodoptera frugiperda) vegetable IPM fact sheet.*
- Malekera, M. J., Acharya, R., Mostafiz, M. M., Hwang, H.-S., Bhusal, N., & Lee, K.-Y. (2022). Temperature-dependent development models describing the effects of temperature on the development of the fall armyworm *Spodoptera frugiperda* (J. E. Smith) (Lepidoptera: Noctuidae). *Insects*, *13*(12), 1084.
- Bayer Crop Science. (n.d.). *How to determine corn growth stages.*
- University of Minnesota Extension. (n.d.). *Corn growth and development.*
- Garcia, A. G., Godoy, W. A. C., Thomas, J. M. G., Nagoshi, R. N., & Meagher, R. L. (2018). Delimiting strategic zones for the development of fall armyworm (Lepidoptera: Noctuidae) on corn in the state of Florida. *Journal of Economic Entomology*, *111*(1), 120–126.
- Early, R., Gonzalez-Moreno, P., Murphy, S. T., & Day, R. (2018). Forecasting the global extent of invasion of the cereal pest *Spodoptera frugiperda*, the fall armyworm. *NeoBiota*, *40*, 25–50.
- Adan, M., Tonnang, H. E. Z., Kassa, C. E. F., Greve, K., Borgemeister, C., & Goergen, G. (2024). Combining temperature-dependent life table data into Insect Life Cycle Model to forecast fall armyworm *Spodoptera frugiperda* (J.E. Smith) distribution in maize agro-ecological zones in Africa. *PLoS ONE*, *19*(5), e0299154.
- Zuefle, M., & Cornell New York State Integrated Pest Management Program. (2026, July 7). *New York sweet corn trap network report: July 7, 2026.* Cornell University.
- Zuefle, M., & Cornell New York State Integrated Pest Management Program. (2026, September 22). *New York sweet corn trap network report: September 22, 2026.* Cornell University.

Full citations with DOIs/URLs are in the "Software" wiki write-up's References section. The identification-guide photographs in `VIPER.html` are individually credited and licensed CC BY 3.0 in their captions.

## Contact

Team VERDANT, Rochester iGEM 2026 — uofr.igem@gmail.com
