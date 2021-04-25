---
title: "Movie Recommender"
permalink: /portfolio/aos-movie-reco/
date: 2021-4-1
excerpt: "Kotlin Project with Movie Recommender"
header:
  image: /assets/images/aMovReco_main_wide.png
  teaser: /assets/images/aMovReco_teaser.gif
company: "Academic Project"
engine: "N/A"
platform: "Android Devices"
skills: "Kotlin, AWS, Python"
role: "Front-End Engineer"
responsibilities: "Android Front-End & Recommendation System"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Features
 - All Front-End Part of Android Development using Kotlin
 - Recommender System using TensorFlow and Python
 - Serving Recommender System using AWS, Docker, automating a model training 

### Android

<figure class="third">
	<img src="/assets/images/aMovReco_aos_login.gif">
  <img src="/assets/images/aMovReco_aos_imgLoad.gif">
	<img src="/assets/images/aMovReco_aos_ui.gif">
</figure>

- **Login System**
  - The fragments are managing under the principles of navigation, tracking each stack.
  - Client and server can handle all cases of failed login attempts sucha as blank data, miss-matched passwords, and wrong ID or password.

- **Image Loading as Skeleton UI**
  - You can see the progress of loading images like firstly as the following order: title with an empty placeholder, colored background, a full image. This type of design is called Skeleton UI. It visually communicates with a user by showing the process of loading. Back in the day, there were only a loading spinner without any other information.
  - Bing Search API is used to search images based on the given movie title from the server
  - Picasso is used to download the found image URLs and to cache the downloaded images.


- **Card Swiping & Grid Layout**
  - A set of recommended movies will be in the card-swiping page after a valid login.
  - In the grid layout page, It shows a list of liked movies the user confirmed.


### Cold Start Problem

In eCommerce, there are two distinct categories of cold start: product cold start and user cold start. In the case of this project, no user history causes a **user cold start**. The system does not know the personal preferences of a new user, but we have nearly over 100,000 rating data from hundreds of users. So, I applied a **popularity-based strategy** using the already existing user data.

<figure>
  <img src="/assets/images/aMovReco_rs_coldStart_pop.png">
</figure>

For new users, the above list is what they will be recommended at first. Another problem was the users will get the same movies in the same order. There were two options to prevent this problem. One is to keep generate a list of trending movies in real-time. This method uses the recorded time data which is on the fourth column of the user data. Another one is just to randomize the order. Due to the latter is much simpler, we chose to randomize all data.

## Used APIs & Libraries
 - Bing Image Search API: It allows to scour the web for images such as thumbnails, website info, metadata, and etc.
 - Picasso: A powerful image downloading and caching library for Android
 - Fuel: The easiest HTTP networking library for Kotlin/Android
 - Card Stack View: Tinder like swipeable card view for Android

## References
 - [Android Architecture Components Samples](https://github.com/android/architecture-components-samples)
 - [Android Advanced Navigation](https://github.com/android/architecture-components-samples/tree/master/NavigationAdvancedSample)
 - [Dataset: MovieLens](https://grouplens.org/datasets/movielens/)
 - [Prototyping a Recommender System](https://towardsdatascience.com/getting-started-with-recommender-systems-and-tensorrec-8f50a9943eef)
 - [Introducing TensorFlow Recommenders](https://blog.tensorflow.org/2020/09/introducing-tensorflow-recommenders.html)
 - [TensorFlow Serving with Docker](https://www.tensorflow.org/tfx/serving/docker)
 
