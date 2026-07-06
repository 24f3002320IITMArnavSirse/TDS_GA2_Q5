# Analytics API

Minimal FastAPI service for aggregating a batch of analytics events.

## Endpoint

`POST /analytics`

The request body must contain an `events` array. Each event has:

- `user`: string
- `amount`: number
- `ts`: integer timestamp

## Authentication

Requests must include the assigned API key in the `X-API-Key` header.
Missing or incorrect keys return HTTP `401`.

## Run Locally

```bash
pip install -r requirements.txt
uvicorn main:app --reload
```

Local endpoint:

```text
http://127.0.0.1:8000/analytics
```

## Deploy To Render

Create a Render Python web service from this repository.

Build command:

```bash
pip install -r requirements.txt
```

Start command:

```bash
uvicorn main:app --host 0.0.0.0 --port $PORT
```

Submit the deployed endpoint URL with `/analytics`, for example:

```text
https://analytics-api.onrender.com/analytics
```

## Curl Tests

Missing API key, expected HTTP status `401`:

```bash
curl -i -X POST http://127.0.0.1:8000/analytics \
  -H "Content-Type: application/json" \
  -d '{"events":[]}'
```

Wrong API key, expected HTTP status `401`:

```bash
curl -i -X POST http://127.0.0.1:8000/analytics \
  -H "Content-Type: application/json" \
  -H "X-API-Key: wrong-key" \
  -d '{"events":[]}'
```

Valid API key and analytics batch:

```bash
curl -i -X POST http://127.0.0.1:8000/analytics \
  -H "Content-Type: application/json" \
  -H "X-API-Key: ak_ilv5gr24z45kpnumzsrffqtb" \
  -d '{
    "events": [
      {"user": "alice", "amount": 42.5, "ts": 1700000000},
      {"user": "bob", "amount": -10, "ts": 1700000001},
      {"user": "alice", "amount": 20, "ts": 1700000002},
      {"user": "charlie", "amount": 100, "ts": 1700000003},
      {"user": "bob", "amount": 50, "ts": 1700000004},
      {"user": "charlie", "amount": 0, "ts": 1700000005}
    ]
  }'
```

Expected successful response:

```json
{
  "email": "24f3002320@ds.study.iitm.ac.in",
  "total_events": 6,
  "unique_users": 3,
  "revenue": 212.5,
  "top_user": "charlie"
}
```
