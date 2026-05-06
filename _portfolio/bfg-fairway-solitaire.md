---
title: "Fairway Solitaire"
permalink: /portfolio/bfg-fairway-solitaire/
date: 2021-09-01
excerpt: "A casual card game based on solitaire with a golf theme."
header:
  image: /assets/images/fairwaySolitaire_main_wide.png
  caption: "[YouTube Link](https://www.youtube.com/watch?v=byKKz8w7CnQ)"
  teaser: /assets/images/fairwaySolitaire_icon.png
category: professional
company: "[Big Fish Games](https://www.bigfishgames.com/us/en/pc-games.html)"
engine: "Customized Cocos2d-JS"
platform: "Android, Fire OS & iOS Mobiles"
skills: "C++, Java, JavaScript"
role: "Software Engineer"
responsibilities: "Android, Gameplay, Server, and etc."
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Assigned Tasks
 - Implementing new game events and maintaining this cross-platform project for Android, Fire OS, and iOS.
 - Driving cross-cutting initiatives in performance, build tooling, and backend cost.
 - Maintaining/Updating 3rd-party SDKs for Android and Fire OS
 - Supporting the Live Ops team, the server side, and iOS.

## Game Description
<figure class="half">
	<img src="/assets/images/fairwaySolitaire_desc_1.png">
	<img src="/assets/images/fairwaySolitaire_desc_2.png">
	<figcaption>Fairway Solitaire presents players with a modified type of solitaire with a golf theme. In the game, players use golf clubs and playing cards to clear the table.</figcaption>
</figure>

## Engineering Highlights

### Startup Time Optimization
The cold-start path was dominated by synchronous audio decoding and third-party SDK initialization on the main thread, leaving players watching a loading screen for nearly eight seconds. I moved audio loading onto an async pipeline so the engine no longer blocks on decoding banks the player won't hear during the splash, and deferred non-critical SDK init (analytics, attribution, ads) until after the first interactive frame. Cold start dropped from **7.8 s to 2.1 s — a ~73% reduction**, directly cutting bounce on cold launches.

### Firebase Realtime Database Cost Reduction
Telemetry showed Firebase Realtime Database read/write traffic running well above what the actual gameplay loops required, with redundant listeners and chatty syncs accounting for most of the bill. I audited and tightened the access patterns — consolidating duplicate reads, removing dead listeners, and reshaping a handful of high-traffic paths — to land a **22.8% cost reduction** with no change to player-facing behavior.

### Android Build System Enhancement
The Android build was a long-standing pain point — full builds were slow enough that engineers were context-switching while waiting. I overhauled the Gradle configuration and surrounding shell scripts to land a **~5× build-time improvement**:
- Modernized the internal Android build tooling and Gradle setup
- Removed deprecated art-optimization steps that no longer earned their cost
- Updated scripts and configuration for the Apple Silicon migration so M-series machines build natively

### Level Design Tool
My team made a new game mode, but it was so painful to position a flag and to set its data. So I made a simple tool for it. It has save/load/export features with many other handy input box to set each course data.

<figure>
  <img src="/assets/images/fairwaySolitaire_tool_1.png">
  <figcaption>A level design tool made with WPF</figcaption>
</figure>

### AI Art Assets

My team wanted to see the possibility of using AI Art Assets so that Fairway can have variation of new art assets with low cost if it's viable. As I had a huge interest in the AI world, I investigated Adobe Firefly and Stable Diffusion and tried train/finetune technologies to generate a set of images with the Fairway art style.

#### Character Training
- Stable Diffusion Dreambooth with Google Colab
- The Initial Mode: Stable Diffusion 1.5
- Class: Prairie Dog
- 5 number of McDivot images were used

<figure>
  <img src="/assets/images/fairwaySolitaire_McDivot_ref_1.png">
  <figcaption>McDivot is the main character in Fairway. This is one of five base images</figcaption>
</figure>

<figure>
  <img src="/assets/images/fairwaySolitaire_McDivot_gen_1.png">
  <figcaption>This is the generated images. I believe it needs more images from many different angles to make it better. Teaching AI a unique character is more difficult than teaching a background.</figcaption>
</figure>

#### Background Training
- Stable Diffusion Dreambooth with Google Colab
- The Initial Mode: Stable Diffusion 1.5
- 13 number of Fairway background images were used
- The training was performed with captions in each base image

<figure class="half">
	<img src="/assets/images/fairwaySolitaire_beach_ref_1.jpg">
	<img src="/assets/images/fairwaySolitaire_mountain_ref_1.jpg">
	<figcaption>Two of thirteen base images</figcaption>
</figure>

<figure>
  <img src="/assets/images/fairwaySolitaire_beach_gen_1.png">
  <figcaption>Generated images: beach theme, air balloons</figcaption>
</figure>

<figure>
  <img src="/assets/images/fairwaySolitaire_mountain_gen_1_vol.png">
  <figcaption>Generated images: mountain theme, volcano eruption</figcaption>
</figure>

### ETC.
 - Automated Theming System: app icon, main menu, social menu
 - Implemented an auto event scheduler on the server side, replacing a manual process
 - Simplified a course generator, decoupling it from external resources
 - and many other things...

## References
 - [Fairway Google Play Store](https://play.google.com/store/apps/details?id=com.bigfishgames.fairwaysolitaireuniversalf2pgoogle&hl=en_US&gl=US)
 - [Fairway Official Site](https://www.bigfishgames.com/us/en/company/fairway-solitaire.html)
 