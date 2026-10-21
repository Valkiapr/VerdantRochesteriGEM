# VIPER — Vigilant Insect Pest Emergence Radar

**Team VERDANT, Rochester iGEM 2026**

### Farmer-friendly estimates of fall armyworm activity

This tool estimates the timeframes when fall armyworm caterpillars are most
likely to be consuming the leaves of corn, based on your local weather and
planting dates. It is available in both Spanish and English.

The model predicts when fall armyworm caterpillars (*Spodoptera frugiperda*)
are probably feeding on leaves instead of the ear. This prototype allows users
to enter their city and planting date and receive predictions on when the corn
will be in different stages of growth and when there will be feeding by
caterpillars on the leaves. The prototype has not been field tested, and should
always be used in conjunction with previous knowledge and experience.

---

## What VIPER does

The unique advantage of VIPER is its ability to forecast when the fall armyworm
caterpillar will be feeding on the corn leaf instead of the corn ear, creating
an operational window for the VERDANT engineered biocontrol circuit (MeSA →
SABP2 → shikimate-pathway amplification → Vip3A, with a theophylline-gated kill
switch).

Users provide a field location and planting date, and VIPER generates:

- Predicted dates for corn growth stages: VE, V6, VT and R1 (emergence,
  six-leaf/whorl, tasseling, silking).
- Forecast windows for fall armyworm leaf feeding (Wave 1, Wave 2, etc.), each
  flagged if it overlaps a silking period, which potentially means ear feeding
  is likely.
- A plain-language summary of the current risk status, and the next predicted
  window for feeding.
- A note indicating whether the results rely on live weather, Open-Meteo's
  historical database, or demo/sample data.

This tool exists in two variations using the same equations:

- `index.html` — a standalone static HTML page for use by growers and evaluators
- `app.py` — a Python/Streamlit prototype for use during development

Changes to constants in one version should be duplicated in the other, as
indicated at the top of each file.

---

## Quick start — static HTML version (recommended)

No installation is required.

1. Clone or download the repository.
2. Open `index.html` in your browser (Chrome, Edge, Safari or Firefox).
3. Choose **Use my location** to get weather updates right now (internet
   required), or **Try sample weather** to see how it works offline.
4. Provide the planting date and, if relevant, the date you first detected fall
   armyworm activity. Click to see predictions.

This version is maintained entirely without a server, build process or hosted
platform. The Principal Investigator on this project has asked to keep it as
source code in the repository, so all growers and assessors run their own
instance.

## Quick start — Python / Streamlit prototype

This version is used for development and to check the output against the static
version. It is not needed for using the tool.

1. Run `pip install -r requirements.txt`
2. Run `streamlit run app.py`
3. A browser tab opens at `http://localhost:8501` with inputs and outputs
   similar to the static version.

**Prerequisites:** Python 3.9+ and the packages in `requirements.txt`
(streamlit, pandas, requests, matplotlib). Not needed for the static HTML
version.

---

## How it works

**Weather data.** The live lookup for historical dates uses actual daily
temperature highs and lows from the Open-Meteo historical archive API. The next
16 days of weather forecast use the Open-Meteo forecast API. If no location
information is available or a live lookup fails, the app falls back to a smooth
synthesized temperature curve (demo mode).

**Corn phenology.** The tool uses a standard growing-degree-day calculation for
corn with a base temperature of 10 °C and a ceiling of 30 °C, using published
thresholds for the four corn growth stages (VE, V6, VT, R1) from U.S. extension
sources (Bayer Crop Science; University of Minnesota Extension).

**Fall armyworm development.** A degree-day model per Malekera et al. (2022):
base 13.51 °C, with 171.5 DD for eggs, 193.8 DD for larvae and 124.7 DD for
pupae per generation. A 45/55 division is used to estimate time spent on the
leaf and whorl (early instars) versus the ear (later instars). This division is
tentative, as no published degree-day-specific early/late instar split exists
(see Limitations).

**Risk-window engine.** The engine combines the corn growth-stage model and the
fall armyworm generation timings into a list of predicted leaf-feeding windows,
flagging any that arrive after the corn silking date.

All biological reasoning for the constants is noted at the top of the
`<script>` section of `index.html`; the corresponding section in `app.py` is the
same.

---

## Limitations

These are also shown inside the tool itself.

- The thresholds use generic values and are not adjustable for hybrid maturity
  ratings.
- The 45/55 early/late instar leaf-vs-ear division is not a published value. The
  split is relatively arbitrary and tentative, and needs to be adjusted in the
  model.
- If a first-sighting date cannot be determined, the tool estimates 30 days
  after planting.
- Forecasts beyond 2–3 weeks rely on weather forecasts, which are naturally more
  uncertain the further ahead they reach.
- The same set of temperature thresholds is used nationwide. In non-Corn Belt
  areas where fall armyworm biology may vary from that of the temperate Corn
  Belt (see Garcia et al. 2018; Early et al. 2018; Adan et al. 2024), dates
  should be interpreted as estimates rather than exact predictions.
- Two validations currently exist separately. VIPER's predicted windows have
  been checked against independent public pest-activity databases (Cornell's NY
  Sweet Corn Trap Network — see the Software write-up, section 4.2), but have
  not yet been validated against lab data on activation of VERDANT's circuit.

---

## Repository contents

| File | Description |
|---|---|
| `index.html` | Static, dependency-free version that runs directly in a browser. |
| `app.py` | Python/Streamlit prototype using the same mathematics as `index.html`. |
| `requirements.txt` | Python dependencies for `app.py`. |
| `figures/` | R scripts generating Figures 9 and 10 of the write-up. |
| `LICENSE` | MIT license. |
| `README.md` | This document. |

---

## Adding a new pest or plant to VIPER

Because the risk-window engine is purposely flexible, this modeling approach
could be applied to other insect and plant combinations — for example diamondback
moth on brassicas, soybean looper, or European corn borer on corn — by replacing
the degree-day ranges, developmental thresholds and detection mechanisms with
those calculated for the new system. The calendar, risk-window and flagging
logic still apply unchanged.

See *Usefulness to other projects* in the Software write-up for more detail.

---

## License

Released under the MIT License. See `LICENSE`.

---

## Citations

The biological constants used and regional comparisons made in this calculator
are sourced from:

- Adan, M., Tonnang, H. E. Z., Kassa, C. E. F., Greve, K., Borgemeister, C., &
  Goergen, G. (2024). Integrating temperature-dependent life table information
  into Insect Life Cycle Model to predict the distribution of fall armyworm
  *Spodoptera frugiperda* (J. E. Smith) in maize agro-ecological zones in
  Africa. *PLOS ONE*, 19(5), e0299154.
- Bayer Crop Science. (n.d.). *How to determine corn growth stages*. Corn expert
  guides.
- Cornell Integrated Pest Management, Cornell University College of Agriculture
  and Life Sciences. (n.d.). *Fall armyworm (*Spodoptera frugiperda*) vegetable
  IPM fact sheet*.
- Early, R., González-Moreno, P., Murphy, S. T., & Day, R. (2018). Forecasting
  the global extent of invasion of the cereal pest *Spodoptera frugiperda*, the
  fall armyworm. *NeoBiota*, 40, 25–50.
- Garcia, A. G., Godoy, W. A. C., Thomas, J. M. G., Nagoshi, R. N., & Meagher,
  R. L. (2018). Delimiting strategic zones for the development of fall armyworm
  (Lepidoptera: Noctuidae) on corn in the state of Florida. *Journal of Economic
  Entomology*, 111(1), 120–126.
- Malekera, M. J., Acharya, R., Mostafiz, M. M., Hwang, H.-S., Bhusal, N., & Lee,
  K.-Y. (2022). Temperature-dependent development models describing the effects
  of temperature on the development of the fall armyworm *Spodoptera frugiperda*
  (J. E. Smith) (Lepidoptera: Noctuidae). *Insects*, 13(12), 1084.
- University of Minnesota Extension. (n.d.). *Corn growth and development*.
- Zuefle, M., & Cornell New York State Integrated Pest Management Program.
  (2026). *New York sweet corn trap network reports*.
  https://sweetcorn.nysipm.cornell.edu/
- Zippenfenig, P. (2023). *Open-Meteo.com weather API* [Computer software].
  Zenodo. https://zenodo.org/records/14582479
