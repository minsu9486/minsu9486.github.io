---
title: "Résumé"
permalink: /resume/
layout: single
header:
  overlay_color: "#000"
author_profile: true
toc: true
toc_sticky: true
---

# Software Engineer
Seattle, WA · [LinkedIn](https://www.linkedin.com/in/mean-kang/) · [Portfolio](/portfolio/)

## Skills

- **Server-side:** Java, GCP, AWS, Docker, Node.js, MongoDB, MySQL, Memcached, Redis
- **Client-side:** C++, JavaScript, C#, Java, Python, Shell Script, WPF, Gradle, PHP

## Experience

### Software Engineer — BFG Entertainment Inc.
*Nov 2025 – Present*

- Executing migration from BrainCloud (JS) to Nakama (Go), reducing backend costs by ~75%
- Optimized startup time 73% (7.8s → 2.1s) via async audio loading and deferred SDK init
- Drove adoption of AI-assisted dev workflows (Claude Code) across frontend and backend
- Automated manual theming process using configurable data and override rules for UI assets
- Modernized a core library, adding Apple Silicon and ARM64 support and migrating GNU to GCC
- Supported infra migration through cross-team testing, client/server diagnostics, error analysis

### Software Engineer — Big Fish Games
*Sep 2021 – Oct 2025*

- Developed cross-platform features (C++, JavaScript) for Android and iOS (+ Web for testing)
- Reduced Firebase Realtime Database usage with a 22.8% cost reduction
- Enhanced Android build system (Gradle, Shell Script), achieving 5x performance improvement
- Improved RESTful APIs (Java) with concurrency and MySQL for new social features
- Implemented an auto event scheduler on the server side, replacing a manual process
- Managed server deployments using Git and GCP's Cloud Build, monitoring server health
- Optimized content generation (Shell Script, PHP) by streamlining/removing unnecessary steps
- Maintained 1st and 3rd-party SDKs for Android (Java, C++, JNI), supporting iOS (Objective-C)
- Implemented a level design tool using WPF, significantly reducing manual design time
- Prototyped the frontend/backend of a web store using React, GraphQL, Adobe Commerce

### Client Engineer — Funigloo
*Nov 2015 – Dec 2016*

- Developed a compatibility pipeline between Java and C++
- Created a multi-language support system in C++
- Integrated external social SDKs and extended features to enhance social content
- Adapted a Japanese project for Southeast Asia, collaborating with external teams

## Independent Projects

### AI Incident Platform
*Kafka, Cassandra, S3, GraphQL, Gemini*

- Built event-sourced + CQRS platform: API publishes to Kafka, consumers project to Cassandra
- Layered effectively-once over at-least-once via LWT dedup, retry-with-backoff, and DLQ
- Isolated failure domains with two independent Kafka consumer groups: projection + enrichment
- Instrumented with pino structured logs and Prometheus end-to-end + consumer-lag metrics
- Built similar-incident search via Gemini embeddings in Cassandra VECTOR with SAI ANN index

### Movie Recommender with AWS
*Kotlin, TensorFlow, Python, AWS, Docker*

- Trained TensorFlow Recommenders on MovieLens 100K, served via Docker on AWS EC2
- Built ratings DB → model pipeline with hourly automated retraining and cold-start fallback
- Built Kotlin Android client: swipe-to-rate UI, REST (Fuel), dynamic poster loading (Bing + Picasso)

### Real-Time Multiplayer Backend
*C++, AWS EC2, Node.js, MongoDB, Redis*

- Split auth issuer (Node) from validator (C++) — no network callback per connect
- Chose dirty-flag delta sync over full-state snapshots; cut state-update bandwidth ~75%
- Built reliable transport over UDP to escape TCP head-of-line blocking on the state-sync path

## Education

**DigiPen Institute of Technology** — *B.S. Computer Science (Real-Time Interactive Simulation)*, Apr 2021
