# Project: Local Weather Pipeline

## What you're building

A small end-to-end data pipeline that:

1. Fetches weather data from a free public API
2. Stores it raw
3. Transforms it using dbt
4. Orchestrates the whole thing with Airflow
5. Runs in a containerized environment with Docker

This is a complete mini data engineering project. Small enough to finish in a few days. Real enough to put on a portfolio and talk about in an interview.

---

## Tools required

- **Docker + Docker Compose** — containerize Airflow and the database
- **Apache Airflow** — orchestrate and schedule the pipeline
- **dbt (Core, not Cloud)** — transform the raw data into a clean model
- **Python** — write the ingestion logic inside the Airflow DAG
- **PostgreSQL** — store raw and transformed data (runs in Docker)
- **Open-Meteo API** — free weather API, no authentication required

---

## The data source

Use the Open-Meteo API. It's free, requires no API key, and returns clean JSON.

Example call — hourly temperature for Mexico City for the last 7 days:

```
https://api.open-meteo.com/v1/forecast?latitude=19.43&longitude=-99.13&hourly=temperature_2m&past_days=7&forecast_days=1
```

Explore the response. Understand its structure before writing any code.

---

## What the pipeline should do

### Step 1 — Ingest

Write a Python function that calls the API and inserts the raw hourly records into a PostgreSQL table called `raw_weather`. Each row should represent one hourly observation and include at minimum:

- Timestamp
- Temperature
- City name
- Ingestion timestamp

### Step 2 — Transform

Write a dbt model called `daily_weather_summary` that reads from `raw_weather` and produces one row per day with:

- Date
- Average temperature
- Maximum temperature
- Minimum temperature

Write at least one dbt test. You decide which one and why.

### Step 3 — Orchestrate

Write an Airflow DAG that:

- Runs the ingestion function
- Triggers the dbt transformation after ingestion completes
- Is scheduled to run daily

### Step 4 — Containerize

Everything — Airflow, PostgreSQL, dbt — should run via `docker-compose up`. Someone should be able to clone your repo and run the pipeline with a single command.

---

## Deliverables

When you're done, you should have:

1. A GitHub repository with a clear README that explains what the project does, how to run it, and the decisions you made
2. A working `docker-compose.yml` that spins up the full environment
3. An Airflow DAG file
4. A dbt project with at least one model and one test
5. A Python ingestion script or function
6. A brief written section in the README titled **"Decisions and tradeoffs"** — explain at least three choices you made and why

The decisions and tradeoffs section is the part that will be evaluated most carefully.

---

## Constraints

- Do not use Airflow's pre-built operators for the API call — write the Python logic yourself inside a `PythonOperator`
- Do not use dbt Cloud — use dbt Core locally inside Docker
- The pipeline must be **idempotent** — running it twice on the same day should not duplicate records. How you achieve this is your decision.
- No tutorial should exist for this exact project — if you find one that does it exactly, add a second city and a comparison model

---

## Evaluation criteria

When you share the project, it will be evaluated on:

- **Does it run?** Not just "does the code look right" — does the full pipeline actually execute
- **Is the dbt model correct?** Does the transformation logic actually produce what it claims to
- **Is the test meaningful?** Did you pick a test that catches something real, or just the easiest possible one
- **Is the README honest?** Does it explain decisions, not just steps
- **Is the pipeline idempotent?** Run it twice, check the row count
- **Can you explain every line?** Specific choices will be questioned

---

## One honest warning

Docker + Airflow local setup is the part that trips most people up. Expect to spend real time on:

- Networking between containers
- Volume mounts
- Getting dbt to connect to PostgreSQL from inside the Airflow container

This is not a bug in your approach — it's the reality of containerized pipelines. When you hit that wall, work through it systematically rather than copying a Stack Overflow answer you don't understand.

---

## Suggested project structure

```
weather_pipeline/
  ├── dags/
  │     └── weather_dag.py
  ├── dbt/
  │     ├── models/
  │     │     └── daily_weather_summary.sql
  │     ├── tests/
  │     ├── dbt_project.yml
  │     └── profiles.yml
  ├── ingestion/
  │     └── fetch_weather.py
  ├── docker-compose.yml
  ├── Dockerfile
  └── README.md
```

This structure is a suggestion, not a requirement. If you have a good reason to organize it differently, do so and explain why in your decisions section.

---

## Definition of done

The project is finished when:

- `docker-compose up` starts all services without manual intervention
- The Airflow DAG runs end to end without errors
- `raw_weather` contains hourly records
- `daily_weather_summary` contains one row per day with correct aggregations
- Running the pipeline twice produces the same row count as running it once
- The README explains what it does, how to run it, and the decisions behind it
- You can explain every line of code without referring to notes

Share the GitHub link when all of the above are true. No partial submissions.
