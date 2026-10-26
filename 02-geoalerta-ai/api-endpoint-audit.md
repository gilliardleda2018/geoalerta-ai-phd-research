# GeoAlerta Sul — Static API Route Audit

**Date:** 2026-10-26. **Source:** private `gilliardleda2018/geoalerta-sul`, branch `main`. **Method:** inspected route decorators and selected handlers in `app/main.py`, `app/municipal_api.py`, `app/maps_main.py` and associated documentation. This is a **static inventory**, not an OpenAPI runtime export or an HTTP integration test. No authentication, latency, response schema or production availability is asserted without runtime tests.

**Inventory:** 40 routes in `app/main.py` (38 GET and 2 POST, including dashboard/health routes) plus 1 municipal route. The map-only application uses a subset of the same handlers and adds its own health/readiness routes. The table below lists 39 non-dashboard main routes plus municipal; dashboard GET `/`, `/proposta`, `/tecnico` are separate and described later. **Correct route totals should be calculated from the enumerated list rather than marketing claims.**

## Health and status

| Method | Path | Observed purpose / qualification |
|---|---|---|
| GET | `/health` | Liveness; process and version, not evidence of data readiness |
| GET | `/ready` | Readiness, source/quality gating; distinguish from /health |

## Terrain, maps and geospatial simulation

| Method | Path | Observed purpose / qualification |
|---|---|---|
| GET | `/api/v1/region` | Pilot regional profile |
| GET | `/api/v1/terrain/metadata` | DEM/HAND terrain metadata |
| GET | `/api/v1/terrain/{z}/{x}/{y}.png` | Terrain raster tile |
| GET | `/api/v1/map/risk` | Risk layer; scenario and limit controls |
| GET | `/api/v1/risk-model/areas` | Official mapped risk sectors |
| GET | `/api/v1/risk-model/summary` | Risk model provenance/limitations |
| GET | `/api/v1/risk-model/iede-summary` | IEDE zoning snapshot provenance |
| GET | `/api/v1/map/iede-zoning` | IEDE risk zoning map layer |
| GET | `/api/v1/map/municipal-stage` | Municipal stage polygon/map |
| GET | `/api/v1/impact/municipal-stage` | Impact for a given stage |
| POST | `/api/v1/twin/simulate` | Temporal digital-twin simulation, not hydraulic-depth simulation |
| GET | `/api/v1/map/copernicus/emsr720` | Copernicus EMSR720 extent/context |

## Source catalog and validation

| Method | Path | Observed purpose / qualification |
|---|---|---|
| GET | `/api/v1/areas` | Configured geographic areas |
| GET | `/api/v1/data-sources` | Source catalog, metadata and trust |
| GET | `/api/v1/risk-sectors/official` | Official SGB sector records |
| GET | `/api/v1/history/atlas` | SEDEC Atlas disaster-history catalog |
| GET | `/api/v1/model/atlas-impact/validation` | Conditional severe-impact model validation |
| GET | `/api/v1/model/hydrological-occurrence/validation` | Hydrological occurrence baseline validation |
| GET | `/api/v1/model/taquari-hydrology/validation` | Taquari hydrology validation |

## Evidence, prediction and exposure

| Method | Path | Observed purpose / qualification |
|---|---|---|
| POST | `/api/v1/evidence/normalize` | Normalize source records, provenance and quality |
| POST | `/api/v1/features/assemble` | Assemble traceable feature snapshot |
| GET | `/api/v1/ml/forecast/latest` | Latest ML river-level forecast; availability gated |
| POST | `/api/v1/terrain/risk-points` | Terrain-based point risk |
| GET | `/api/v1/exposure/priorities` | Exposure/priority estimation |
| GET | `/api/v1/map/population-risk` | Population risk layer |
| GET | `/api/v1/location/context` | Evidence dossier for geographic point |
| GET | `/api/v1/evacuation/candidate-route` | Unverified candidate route; not safe evacuation instruction |
| GET | `/api/v1/indicators/latest` | Latest composite indicators |

## Climate, sensors and satellite

| Method | Path | Observed purpose / qualification |
|---|---|---|
| GET | `/api/v1/climate/latest` | Climate intelligence and source context |
| GET | `/api/v1/climate/earth2/latest` | Earth-2 context only; not weighted into core forecast |
| GET | `/api/v1/sensors/dcrs/latest` | RS Civil Defense sensor snapshot |
| GET | `/api/v1/sensors/ana/latest` | ANA telemetry reference |
| GET | `/api/v1/sar/coverage/latest` | SAR coverage metadata |
| GET | `/api/v1/rain-proxy/latest` | Satellite rain proxy |
| GET | `/api/v1/rain/cptec-merge/latest` | CPTEC multi-source rainfall context |

## Critical event and forecasting

| Method | Path | Observed purpose / qualification |
|---|---|---|
| GET | `/api/v1/events/critical/latest` | Critical threshold probability and quality gates |
| GET | `/api/v1/model/critical/validation` | Critical-event model validation report |
| POST | `/api/v1/forecast` | Transparent baseline forecast from supplied observations |

## Municipal internal

| Method | Path | Observed purpose / qualification |
|---|---|---|
| GET | `/api/v1/municipal/{municipality}/decision` | Internal decision snapshot with mandatory human review |

## Additional user interfaces and isolated service

- `GET /`, `GET /proposta`, `GET /tecnico`: HTML dashboards; not separate ML services.
- `app/maps_main.py`: cartographic-only FastAPI service that selects routes from `app/main.py`, with `GET /health` and `GET /ready` of its own. Check which runtime process and container are deployed before describing public exposure.
- FastAPI auto-generated `/docs`, `/redoc`, `/openapi.json` may be present under standard configuration; these are framework-generated, not hand-declared business routes. Verify actual exposure and access controls.

## Findings and audit limitations

1. Routes exist in source; no claim that all respond successfully in deployed AWS.
2. Most `latest` endpoints depend on external snapshots and may return unavailable/503 by design.
3. `/health` cannot substitute for `/ready` or source freshness verification.
4. Public warning/evacuation authorization is expressly excluded; `candidate-route` is not a verified safe route.
5. Endpoint security is configurable: `app/main_secured.py` attaches middleware under feature flags; confirm actual entrypoint and flags in deployment.
6. A full contract audit still requires runtime OpenAPI, response schemas, example payloads, failure modes, authorization matrix, rate limits, source lineage, load tests and security tests per endpoint.

**Source file paths:** `app/main.py`, `app/municipal_api.py`, `app/maps_main.py`, `app/main_secured.py`, `docs/SAFETY.md`.
