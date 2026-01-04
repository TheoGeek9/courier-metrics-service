# courier-metrics-service

## Problem Statement
Couriers and dispatchers need quick daily metrics (stops completed, total miles, time on route, estimated costs) without manually calculating from logs.

## Solution Overview
This service exposes a small REST API that reads delivery logs (CSV or local DB), computes daily metrics, and returns them as JSON. It is designed with a production-like structure (separation of API, services, and data access).

## Architecture
- **Input:** Delivery logs (CSV for v0; SQLite/Azure SQL later)
- **Processing:** Python service layer computes metrics
- **Output:** FastAPI endpoints return JSON
- **Future:** Persist metrics + serve Power BI / dashboard-ready datasets

## Tech Stack
- Python 3.x
- FastAPI
- Uvicorn
- CSV (v0), SQLite (v1)
- Optional later: Azure SQL, ADF/Synapse, Power BI

## API Endpoints
| Method | Endpoint | Description |
|------|---------|-------------|
| GET | /health | Service health check |
| GET | /metrics/daily?date=YYYY-MM-DD | Returns daily metrics for a given date |
| GET | /metrics/range?start=YYYY-MM-DD&end=YYYY-MM-DD | Returns metrics for a date range (optional v1) |

## Data Model (v0)
Delivery log record fields:
- delivery_id (string)
- driver_id (string)
- route_date (YYYY-MM-DD)
- stops_completed (int)
- miles (float)
- start_time (HH:MM)
- end_time (HH:MM)
- fuel_cost (float) (optional)
- notes (string) (optional)

## Metrics Definition
Daily metrics (for a specific date):
- total_stops = sum(stops_completed)
- total_miles = sum(miles)
- total_routes = count(distinct driver_id) or count(rows)
- avg_miles_per_route = total_miles / total_routes
- avg_stops_per_route = total_stops / total_routes
- total_hours = sum(end_time - start_time) across routes

## Setup Instructions
1. Create a virtual environment
2. Install dependencies from requirements.txt
3. Run the API locally
4. Call endpoints in browser or Postman

## Known Limitations (v0)
- No authentication
- CSV-only (no database persistence yet)
- Minimal validation and error handling
- Assumes clean input data

## Future Enhancements
- SQLite/Azure SQL storage
- Schema validation (Pydantic)
- More metrics (ETA accuracy, stops/hour, cost per mile)
- Driver-level endpoint (/drivers/{id}/metrics)
- CI tests + linting
