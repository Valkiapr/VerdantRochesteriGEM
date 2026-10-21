#Verdant Rochester iGEM: Farmer-Friendly Estimates of Fall Armyworm Activity

This tool estimates the timeframes when fall armyworm caterpillars are most likely to be consuming the leaves of corn based on your local weather and planting dates. It's available in both Spanish and English.

The model predicts when fall armyworm caterpillars (Spodoptera frugiperda) are probably feeding on leaves instead of the ear. This prototype allows users to enter their city and planting date and receive predictions on when the corn will be in different stages of growth and when there will be feeding by caterpillars on the leaves. The prototype has not been field tested, and should always be used in conjunction with previous knowledge and experience.

#VIPER – Vigilant Insect Pest Emergence Radar

#VERDANT Team Rochester iGEM 2026

The unique advantage of VIPER is its ability to forecast when the fall armyworm caterpillar will be feeding on the corn leaf instead of the corn ear, creating an operational window for the VERDANT engineered biocontrol circuit (MeSA SABP2 shikimate-pathway amplification Vip3A with a theophylline-gated kill switch). Users provide a field location and planting date, and VIPER will generate:
- (Predicted date for corn stages of growth: VE, V6, VT, R1; emergence, six-leaf/whorl, tasseling, silking).
- Forecast windows for fall armyworm leaf feeding (Wave 1, Wave 2, etc.), each of which was flagged if it overlapped with a silking period, which potentially meant ear feeding was likely.
- Plain-language summary of the current risk status, and the next predicted window for feeding.
- A hint if the results rely on live weather, Open-Meteo's historical database or demo/sample data.

This tool exists in two variations using the same equation, a standalone static html page (index.html, formerly VIPER.html) for use by growers and evaluators, and a python/streamlit prototype (app.py) for use during development. Changes to constants in one version should be duplicated in the other – as indicated at the top of each file.

Quick Start - Static HTML Version (Recommended)
No installation is required.

1. Clone or download the repository.
2. Open VIPER.html in your browser (e.g. Chrome, Edge, Safari, Firefox).
3. Choose "Use my location" to get weather updates right now (internet required) or "Try sample weather" to see how it works offline.
4. Provide the planting date and if relevant, the date you first detected fall armyworm activity. Click to see predictions.

This instance is maintained separately, entirely without a server, build process, or hosted platform. The Principal Investigator on this project has asked to keep it as source code on Github, so all growers/assessors are operating their own instance.

Quick Start - Python / Streamlit Prototype

This version is used for development, and to check the output against the static version; not needed for using the tool.

1. Execute pip install -r requirements.txt.
2. Run streamlit run app.py.
3. This generates a browser tab that is accessible at http://localhost:8501 with inputs and outputs similar to the static version.

Prerequisites Python 3.9+ and the packages from requirements.txt (streamlit, pandas, requests, matplotlib). Not needed for the static HTML version.

How it Works

- The live lookup for historical dates will use actual daily temperature highs and lows from the Open-Meteo historical archive API (when querying for a specific date range, e.g. between 2022-01-01 and 2022-02-01). The next 16 days of weather forecast will use data from the Open-Meteo forecast API. In case of no location information or failed live lookup, the app will fallback to a smooth synthesized temperature curve (demo mode) to provide some data.

- This tool is based on a standard growing-degree-day calculation for corn with a base temperature of 10°C and a ceiling of 30°C using published thresholds for the four growth stages of corn (VE, V6, VT, R1) published by U.S. extension sources (Bayer Crop Science; University of Minnesota Extension).

- This version uses a degree-day (base 13.51°C; 171.5 DD for eggs, 193.8 DD for larvae, and 124.7 DD for pupae per generation) model per Malekera et al. (2022). A 45/55 division is used to guess the time spent on the leaf/whorl (early instars) versus the ear (later instars); this division is tentative, as no published degree-day specific early/late instar split exists (see Limitations).

- The engine combines the modeling of the corn growth stage and fall armyworm generation timings into a listing of leaf-feeding windows that are predicted to occur and any that would arrive after the corn silking date.

All biological reasons for constants are noted right on top of the code at the <script> section of VIPER.html (the section in app.py is the same).

Limitations (Also Shown in the Tool Itself)

- These use generic values and are not adjustable for hybrid maturity ratings.
- The division of early and late-instar leaf-vs-ear at 45/55 is not a published value (early/late division is relatively arbitrary and 45/55 division is tentative) which needs to be adjusted in the model.
- If a first-sighting date cannot be determined, the tool estimates 30 days after planting.
- Forecasts >2-3 weeks are based on weather forecast that is naturally more uncertain the further we try to forecast it.
- The same set of temperature thresholds is used nationwide. In the non-Corn Belt areas where fall armyworm biology may vary from that of the temperate Corn Belt (see Garcia et al. 2018, Early et al. 2018, Adan et al. 2024), dates should be interpreted as estimates rather than exact predictions.
- These two validations currently exist separately. The predicted windows calculated by VIPER have been checked with independent public pest-activity databases (Cornell's NY Sweet Corn Trap Network -- see "Software" write-up, section 4.2) but not yet validated with lab data in regards to activation of VERDANT's circuit.

Repository Contents

- VIPER.html: Version that is static and does not require dependency, can be run directly from the browser.
- App.py: The Python/Streamlit prototype the same mathematics as VIPER.html.
- requirements.txt: Python dependencies specific to app.py.
- README.md: This document.

Adding a new pest or plant to VIPER

Because the risk-window engine is purposely flexible, this modeling approach could be applied to future insect/plant combinations (e.g. Diamondback moth on brassicas, soybean looper, or European corn borer on corn) by appropriate replacement of degree-day ranges, developmental thresholds, and detection mechanisms with those calculated for the new circuit. As stated above, the calendar, risk-window and flagging logic still apply. See Usefulness to other projects in the Software write-up for more details.

Citations

The biological constants used and regional comparisons made in this calculator are sourced from:

- Cornell Integrated Pest Management, Cornell University College of Agriculture and Life Sciences. (n.d.). Fall armyworm (Spodoptera frugiperda) vegetable IPM fact sheet.
- Malekera, M. J., Acharya, R., Mostafiz, M. M., Hwang, H.-S., Bhusal, N., & Lee, K.-Y. (2022). Temperature-dependent development models for the description of the effects of temperature on development of the fall armyworm Spodoptera frugiperda (J. E. Smith) (Lepidoptera: Noctuidae). Insects, 13(12), 1084.
- No date. How to determine corn growth stages. Corn expert guides. Bayer Crop Science.
- University of Minnesota Extension. (n.d.). Corn growth and development.
- Garcia, A. G., Godoy, W. A. C., Thomas, J. M. G., Nagoshi, R. N., & Meagher, R. L. (2018). Delimiting strategic zones for the development of fall armyworm (Lepidoptera: Noctuidae) on corn in the state of Florida. Journal of Economic Entomology, 111(1), 120-126.
- Early, R., Gonzalez-Moreno, P., Murphy, S. T., & Day, R. (2018). Predicting the global distribution of the invasive cereal pest Spodoptera frugiperda, the fall armyworm. Neo Biota, 40, 25-50.
- Adan, M., Tonnang, H. E. Z., Kassa, C. E. F., Greve, K., Borgemeister, C. & Goergen, G. (2024). Integrating temperature-dependent life table information into Insect Life Cycle Model to predict the distribution of fall armyworm Spodoptera frugiperda (J.E. Smith) in maize agro-ecological zones in Africa. PLoS ONE 19(5): e0299154.
- Zuefle, M., & Cornell New York State Integrated Pest Management Program. (2026, July 7).
