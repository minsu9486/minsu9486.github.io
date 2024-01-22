---
title: "Fairway Solitaire"
permalink: /portfolio/bfg-fairway-solitaire/
date: 2021-09-01
excerpt: "A casual card game based on solitaire with a golf theme."
header:
  image: /assets/images/fairwaySolitaire_main_wide.png
  caption: "[YouTube Link](https://www.youtube.com/watch?v=byKKz8w7CnQ)"
  teaser: /assets/images/fairwaySolitaire_icon.png
professional: true
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
 - Maintaining/Updating 3rd-party SDKs for Android and Fire OS
 - Supporting the Live Ops team, the server side, and iOS.

## Game Description
<figure class="half">
	<img src="/assets/images/fairwaySolitaire_desc_1.png">
	<img src="/assets/images/fairwaySolitaire_desc_2.png">
	<figcaption>Fairway Solitaire presents players with a modified type of solitaire with a golf theme. In the game, players use golf clubs and playing cards to clear the table.</figcaption>
</figure>

## My Other Features

### Level Design Tool
My team made a new game mode, but it was so painful to position a flag and to set its data. So I made a simple tool for it. It has save/load/export features with many other handy input box to set each course data.

<figure>
  <img src="/assets/images/fairwaySolitaire_tool_1.png">
  <figcaption>A level design tool made with WPF</figcaption>
</figure>

### AI Art Assets

My team wanted to see the possibility of using AI Art Assets so that Fairway can have variation of new art assets with low cost if it's viable. As I had a huge interest in the AI world, I investigated Adobe Firefly and Stable Diffusion and treid train/finetune technologies to put the Fairway art style onto generated images.

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
  <figcaption>Generated images</figcaption>
</figure>

I believe it needs more images from many different angles to make it better. Teaching AI a unique character is more difficult than teaching a background.

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
- Simplified Course Generator
- Faster Building System
    - Ancient Android internal System Updates
    - Deprecated Art Optimization Removal
    - Old Script/Configuration Updates for Apple Silicon Migration

## References
 - [Fairway Google Play Store](https://play.google.com/store/apps/details?id=com.bigfishgames.fairwaysolitaireuniversalf2pgoogle&hl=en_US&gl=US)
 - [Fairway Official Site](https://www.bigfishgames.com/us/en/company/fairway-solitaire.html)
 