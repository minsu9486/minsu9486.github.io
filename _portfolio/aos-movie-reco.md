---
title: "Movie Recommender"
permalink: /portfolio/aos-movie-reco/
date: 2021-4-1
excerpt: "A TensorFlow Recommenders movie recommendation service with cold-start handling, Dockerized TF Serving on AWS, and a Kotlin/Android client."
header:
  image: /assets/images/aMovReco_main_wide.png
  teaser: /assets/images/aMovReco_teaser.gif
company: "Academic Project"
engine: "N/A"
platform: "Android Devices"
skills: "TensorFlow, TensorFlow Recommenders, Python, AWS, Docker, Kotlin"
role: "Software Engineer"
responsibilities: "Recommender System, Serving Infra, Android Client"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Overview
A movie recommendation service built on **TensorFlow Recommenders**, trained on the MovieLens 100k ratings dataset and served via **TensorFlow Serving** in Docker on AWS. A Kotlin/Android client consumes the model's outputs through a thin REST surface. The pipeline includes an automated retraining loop so new ratings flow back into the deployed model artifact, and a popularity-based cold-start strategy handles users with no interaction history.

## Architecture
<figure>
	<img src="/assets/images/aMovReco_main_wide.png">
</figure>

The system splits along the standard offline / online boundary. **Offline:** the MovieLens ratings dataset feeds a TFRS trainer that produces a SavedModel artifact; retraining is automated so the artifact can be refreshed without code changes. **Online:** TF Serving loads the artifact in a Docker container on AWS, fronted by a small backend that serves the Android client. The client receives `(movie_id, title, genres)` tuples and resolves posters separately via Bing Image Search — keeping the recommendation path independent of the image-rendering path.

## Key Features
 - Two-stage **retrieval-then-ranking** recommender built with TensorFlow Recommenders on MovieLens 100k ratings
 - **Cold-start handling** for new users via a popularity-based seed list with randomized ordering, so no two new users see an identical opener
 - **TF Serving in Docker** as the inference layer, fronted by an AWS-hosted backend — model artifact is the deployable unit
 - **Automated retraining** pipeline so newly-collected ratings refresh the served model without manual intervention
 - Kotlin/Android client with skeleton-UI image loading and persistent poster URL caching

## Implementation Highlights
 - **Cold-start strategy:** chose randomized popularity ordering over real-time trending recomputation. Both options solve "new users seeing identical lists" — randomization is simpler, requires no streaming state, and is indistinguishable from trending at this dataset's scale (~100k ratings, hundreds of users).
<figure>
  <img src="/assets/images/aMovReco_rs_coldStart_pop.png">
</figure>
 - **Decoupled poster resolution:** the recommender response carries only `(movie_id, title, genres)`. The client resolves posters via Bing Image Search and caches the URL plus accent color on the `CardMovie` model so repeat renders skip the network entirely (`homescreen/CardMovie.kt`, `listscreen/Leaderboard.kt`). A Gemini-style "single response, all assets inline" path was rejected to keep the inference response cheap and the image-loading concern out of TF Serving.
 - **Server-side pagination** on `/likedMovies` via `start`/`end` range parameters keeps client memory bounded as the user's liked-movies grid grows (`Leaderboard.kt`).
 - **TF Serving over a custom Flask wrapper:** retraining becomes a swap-the-artifact operation rather than a code redeploy, and the gRPC/REST contract is fixed by the model signature.

### Client (Android)

<figure class="third">
	<img src="/assets/images/aMovReco_aos_login.gif">
  <img src="/assets/images/aMovReco_aos_imgLoad.gif">
	<img src="/assets/images/aMovReco_aos_ui.gif">
</figure>

- **Login & navigation stack** — fragments managed via Android Navigation Component, with client/server validation covering blank fields, mismatched passwords, and unknown users.
- **Skeleton-UI image loading** — three-stage render (title placeholder → accent-color background → full poster) gives users continuous visual feedback while Bing Image Search resolves and Picasso downloads/caches the poster.
- **Card swiping + grid liked-movies** — recommendations land on a swipeable card stack; confirmed likes appear in a paginated grid view.

## Tech Stack
 - **Modeling:** TensorFlow, TensorFlow Recommenders, Python
 - **Serving:** TensorFlow Serving, Docker, AWS
 - **Data:** MovieLens 100k ratings dataset
 - **Client:** Kotlin, Android Navigation Component, Picasso, Fuel
 - **External APIs:** Bing Image Search (poster lookup)

## References
 - [Dataset: MovieLens](https://grouplens.org/datasets/movielens/)
 - [Introducing TensorFlow Recommenders](https://blog.tensorflow.org/2020/09/introducing-tensorflow-recommenders.html)
 - [Prototyping a Recommender System](https://towardsdatascience.com/getting-started-with-recommender-systems-and-tensorrec-8f50a9943eef)
 - [TensorFlow Serving with Docker](https://www.tensorflow.org/tfx/serving/docker)
 - [Android Architecture Components Samples](https://github.com/android/architecture-components-samples)
 - [Android Advanced Navigation](https://github.com/android/architecture-components-samples/tree/master/NavigationAdvancedSample)
