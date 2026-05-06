---
title: "AI Incident Intelligence"
permalink: /portfolio/ai-incident-intel/
date: 2026-04-27
excerpt: "An event-sourced incident intelligence platform with Kafka, Cassandra vector search, and Gemini-powered triage."
header:
  image: /assets/images/aiIncidentIntel_main_wide.png
  teaser: /assets/images/aiIncidentIntel_teaser.png
company: "Personal Project"
platform: "Node.js / Docker Compose"
skills: "Node.js, Apache Kafka, Cassandra 5, Google Gemini, GraphQL, Docker"
role: "Software Engineer"
responsibilities: "Architecture, Implementation"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Overview
An event-sourced incident intelligence platform built around Kafka, Cassandra, MinIO, and GraphQL, with a Gemini-backed AI triage layer. The write path produces events to Kafka — the source of truth — and two independent consumer groups project those events into query-shaped Cassandra tables. Apollo Server serves the read side over GraphQL and a small REST surface.

## Architecture
<figure>
	<img src="/assets/images/aiIncidentIntel_main_wide.png">
</figure>

Kafka is the source of truth. The projection consumer subscribes to `incident-events` and `incident-enriched` and writes into query-optimized Cassandra tables (incident-by-id, by-team, by-severity, service-health, artifacts, embeddings). The enrichment consumer is an independent group on the same source topic — it computes team assignment, normalizes severity, and emits `INCIDENT_ENRICHED` to a separate topic. Decoupling the two means an enrichment outage cannot block the core read path.

## Key Features
 - Structured AI incident summaries (Gemini 2.5 Flash with structured-JSON output: customer impact, likely root cause, confidence, next actions, signals)
 - Similar-incident retrieval via cosine ANN over 768-dim Gemini embeddings, indexed with Cassandra 5 SAI
 - RAG-driven recommended actions over a 7-runbook seed corpus
 - GraphQL + REST surface (Apollo Server + Express)

## Implementation Highlights
 - CQRS-style projections from a single Kafka source-of-truth topic, with two independent consumer groups
 - Idempotent consumers with linear-backoff retry (3 attempts), LWT-backed dedup in `processed_messages`, DLQ on exhaustion, and a manual replay tool
 - Compound partition key on `incidents_by_severity` (`severity_bucket`, `day_bucket`) so the `CRITICAL` bucket cannot grow into an unbounded partition
 - Best-effort embedding writes inside the projection consumer — a Gemini outage logs and skips, never blocking the core projection
 - Per-message correlation IDs (`incidentId`, `eventId`, `topic`, `partition`, `offset`) via pino, plus partition-aware `kafka_consumer_lag_messages` gauge refreshed every 10s on Prometheus

## Tech Stack
 - **Runtime:** Node.js 18+, Express, Apollo Server
 - **Streaming:** Apache Kafka (KRaft, single-node for local dev)
 - **Storage:** Cassandra 5 with SAI vector index, MinIO for artifact blobs
 - **AI:** Google Gemini 2.5 Flash + `embedding-001`
 - **Observability:** pino structured logging, prom-client metrics on three ports
 - **Tests:** Jest integration test against the compose harness, plus per-feature smoke scripts
 - **Infra:** Docker Compose (Kafka, Cassandra, Kafka UI, MinIO)
