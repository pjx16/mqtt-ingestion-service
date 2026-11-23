# MQTT Ingestion Service

A prototype that ingests device telemetry over MQTT and stores it in Postgres **exactly once per logical message**, even when the broker, the network, or the service fails.

The goal is to answer one question with measurements: *what does each MQTT QoS level actually guarantee, and how many messages are lost or duplicated under failure?*

## What it does

- Subscribes to `devices/+/telemetry` on an MQTT broker (Mosquitto or EMQX, run in Docker).
- Validates each payload.
- Writes it to Postgres with an idempotency key of `(device_id, sequence_number)`, so redelivered or reordered messages never create duplicate rows.
- Includes experiments that inject failures (broker or service killed mid-stream, duplicated and out-of-order messages, 10x load, 1,000 simultaneous reconnects) and records loss and duplicate counts.

## Planned stack

- Go for the ingestion service, emulator, and experiment tools
- `paho.mqtt.golang` for the MQTT client
- Postgres, accessed with `pgx`
- Docker Compose for the broker and database
- `go test` for unit tests and `rapid` (`pgregory.net/rapid`) for property-based tests, the Go counterpart to fast-check

## Status

Phase 1 (weeks 1–3) is in planning. No code exists yet.

## Deliverables

1. The repo with the ingestion service, emulator, experiments, and tests.
2. A write-up, "What each QoS level actually guarantees", with measured loss and duplicate counts.

## Requirements

- Go 1.22 or later
- Docker (for the broker and Postgres)
- `psql` (optional, for inspecting the database)
