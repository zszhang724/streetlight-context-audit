# Data, code, model rights and attribution

**No new license has been selected for author-owned code, synthetic data, checkpoints or documentation.** This is a review candidate. Authors must confirm rights and select explicit compatible licenses before any public upload. File presence is not a redistribution grant. There is no invented repository URL or DOI.

## Synthetic data and weather

A/B use the existing seed-1337 synthetic dataset. C uses the archived G0/G1 generated instances and protocol manifests. Synthetic observations, fault injections, labels, seeds and derived model weights require author rights confirmation. External weather is included only because its provider gives an explicit data reuse license; this does not resolve the authors' own licensing decision.

The weather file `data/external/weather/open_meteo_brisbane_2023_hourly.csv` is linked by the existing `results/reports/data_preparation_status.json` to Open-Meteo's historical archive API for Brisbane, 2023. Attribution: **Weather data by Open-Meteo.com, licensed under CC BY 4.0**. See [Open-Meteo terms](https://open-meteo.com/en/terms), [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/), and [historical API documentation](https://open-meteo.com/en/docs/historical-weather-api). Provider terms were checked on 2026-10-29. API service access restrictions and the data reuse license are distinct. Preserve the attribution and source notice with copies; disclose transformations into 15-minute/interpolated/context features using the archived generator. A fresh query need not produce byte-identical weather data; the saved file/hash is the reproduction input.

The recorded query is:
`https://archive-api.open-meteo.com/v1/archive?latitude=-27.4679&longitude=153.0281&start_date=2023-01-01&end_date=2023-12-31&hourly=temperature_2m,relative_humidity_2m,precipitation,rain,wind_speed_10m,wind_gusts_10m,cloud_cover,weather_code&timezone=Australia%2FBrisbane`

## Third-party implementations

Existing license/attribution files from `external_repos/TranAD`, `Anomaly-Transformer`, `TSB-AD` and `Time-Series-Library` are retained wherever present in the inventory. TranAD's existing notice is BSD-3-Clause; Anomaly-Transformer's is MIT. Read each actual retained notice for its full obligations. The publication uses local adaptations; this is not a claim of faithful official execution. The archive does not vendor the entire upstream repositories or grant rights beyond their actual licenses.

No LICENSE file was found at the inspected CATCH repository root. The local CATCH-related implementation's derivation and redistribution permissions require author review. USAD and other adapted baseline attribution/source lineage also require review where no specific local license evidence exists. Retaining a neighboring repository's LICENSE does not automatically license a different adaptation. Before release, confirm which retained code was copied/adapted and place its correct notices alongside it.

## Real/external datasets withheld

The separate `experiments/R2_2_public_real_data/` payload (including its downloaded datasets and derived artifacts) is excluded pending dataset-specific redistribution/derivative permission review. HEED/SILF/Nepal streetlight resources and any Kaggle source mentioned in old preparation reports are not licensed by this package. Existing source records identify HEED and SIL-IoT raw releases as CC BY 4.0, Northern Ireland as OGL, and Twente as lacking an explicit located license. They are excluded here as separate real-data evidence with no completed payload-specific derivative/source review, not because all of their licenses are unknown. See EXCLUDED_REAL_DATA_SOURCES.md. Follow those provider access instructions and obtain any required permission; do not assume that download availability permits rehosting. The local candidate inventory records exclusions. This package cannot reproduce those separate real-data conclusions by itself.

No authentication cache, SSH key, personal session archive, credentials, proxy configuration or unrelated personal data is intended for inclusion. Checkpoints were inspected statically without unsafe deserialization. This inspection is bounded and does not constitute a guarantee against every possible sensitive string or malicious pickle operation; only load trusted, hash-verified weights in a controlled environment.
