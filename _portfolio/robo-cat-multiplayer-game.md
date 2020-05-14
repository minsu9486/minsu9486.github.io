---
title: "Robo Cat, Multiplayer Game"
permalink: /portfolio/robo-cat-multiplayer-game/
date: 2020-04-10
excerpt: "This project is mainly focused on latency handling and three-tier architecture"
header:
  image: /assets/images/roboCat_wide_image.png
  caption: "Used 3rd-party services in this project"
  teaser: /assets/images/roboCat_teaser.png
company: "Academic project"
engine: "SDL from [*Multiplayer Game Programming*](https://www.amazon.com/Multiplayer-Game-Programming-Architecting-Networked-ebook/dp/B0189RXWJQ)"
platform: "Windows & Linux"
skills: "Amazon EC2, C++, Node.js"
role: "Server engineer"
responsibilities: "HTTP user service, latency handling, network security"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Features
 - HTTP user service (data tier)
 - Latency handling (presentation and logic tiers)
 - Data and game connection security

### HTTP User Service 
 - *<u>Amazon EC2</u>* is used to consistently host the user service.
 - *<u>MobaXterm</u>* is used to easily access EC2 from my Windows PC.
 - *<u>PM2</u>* is used for a resilient behavior of the user service.
 - *<u>Postman</u>* is used to automatically test the user service API.
 - *<u>Redis</u>* is used to manage session data which is a temporary type.
 - *<u>MongoDB</u>* is used to preserve user data.

<figure class="half">
	<img src="/assets/images/roboCat_1_ecosys.png">
	<img src="/assets/images/roboCat_1_postman.png">
  <figcaption>1) Ecosystem file of PM2. &emsp;&emsp;&emsp;&emsp; 2) Unit tests through Postman.</figcaption>
</figure>

### Latency Handling
 - *<u>Amazon EC2</u>* is used to consistently host the game sever.
 - *<u>C++ REST SDK</u>* is used to use HTTP protocol in the *C++* client.

If latency problem is not hendled on the client side, users can experence lagging response. One way to hide this problem is temporarily creating a dumy data in the client. The dumy data will be immediately deleted as soon as the real data is arrived from the game server. See the below example where a cat is deploying a bomb by the user's input.

<figure class="half">
	<img src="/assets/images/roboCat_2_latency_bomb_.gif">
	<img src="/assets/images/roboCat_2_latency_handling_bomb.gif">
  <figcaption>See the response time when the cat deploys a bomb after a input number is appeared in "Pressed Key". &emsp;&emsp; 1) No latency hendling. &emsp;&emsp; 2) Responsive reaction.</figcaption>
</figure>

The above simple trick is what I added to the game engine, and other techniques such as *dead reckoning* and *data compression* were already implemented by the engine author.

### Data and Game Connection Security
 - *<u>Nginx</u>* is used to secure SSL keys and certificates and to upstream the user service.
 - *<u>OpenSSL</u>* is used to decode encrypted data in the *C++* server.

Authentication is kept checking from the user service, and SHA-256 encryption is used between the three layers. I will briefly show how it is progressing to secure the user service and game connection.

1. After a client requests login to the user service. The user service creates a unique session and a unique token, then notify it to the client. The two unique data will be used to verify on every http request. 10s of lifespan is assigned to the session as soon as it created.

    ```js
    // Encode the given message with SHA-256 hash algorithm.
    BuildToken:function(message) {
      let hash = crypto.createHash('sha256');
      hash.update(message);
      let token = hash.digest('base64');
      return token;
    },

    LoginUser:function(req, res) {
      ...
      // Creates authentication data.
      var session = base64url(username);
      var token = this.BuildToken(
       (Math.random() * date.getTime());
      
      // Store the session to Redis with 10s of expiration.
      // Store the token to MongoDB.
      ...

      // Respone to the client.
      res.json({
      "session": session,
      "token": token
      });
    }
    ```

2. If the user is verify by successful login, the client requests the address of the game server to the user service.

    ```cpp
    void NetworkManagerClient::UpdateConnect() {
      ...
      // This given data from the user service will be used
      // for verification.
      postData[L"session"] = mUserData.session;
      postData[L"token"] = mUserData.token;

      // Send the connection request to the user service.
      client.request(
        web::http::methods::POST,
        U("/api/v1/connect"), postData)
      ...
    }
    ```

3. The user service gives the server address and a unique connect token to the client. This token is encrypted with SHA-256 and a secret key which is only shared with the game server.
    ```js
    async ConnectToGameServer(req, res) {
      // Check authentication by the given session and token.
      ...

      // The game server address that client will connect.
      var server = process.env.GAME_SERVER;
      // This token will be used when the client tries to
      // connect to game server. The formula is to make sure
      // that the connect token is unique and disposable.
      var tokenConnect = this.BuildToken(
        model.username + model.avatar + SHARED_SECRET);

      // Respone to the client.
      res.json({
      ...
      "server": server,
      "token": tokenConnect
      });
    }
    ```

4. After the client sends the given user data and the given connect token to the game server. The game server decrypts the connect token using the shared secret key. If the decryped token is the same data which is a combination of name, avatar, and the secret key, the game server regards the user as a secured user.

    ```cpp
    void NetworkManagerServer::HandlePacketFromNewClient(...) {
      ...
      // Concatenate the given values to build the message.
      string message = name + avatar + mSharedSecret;
      string digest = digestBuilder.BuildDigest(message);

      // Verify that this login request passes through
      // the authentic user service.
      if (token.compare(digest) != 0) {
       LOG("Token verification has been failed: %s", name);
       return; // Deny the connection.
      }

      // The connection token is verified!
      // Connect the client to this server.
      ...
    }
    ```

## Sorce Code
 - User Service (Data Tier)
   - [ecosystem.config.js](/scripts/ecosystem.config.js/): It defines environment variables.
   - [roboCat_UserData.js](/scripts/roboCat_UserData.js/): It is a module that manages all user data.
   - [roboCat_UserService.js](/scripts/roboCat_UserService.js/): It gives RESTful API using modules.
 - Game Server (Logic Tier)
   - [NetworkManagerServer.cpp](/scripts/rc-networkManagerServer.cpp/)
 - Game Client (Presentation Tier)
   - [NetworkManagerClient.cpp](/scripts/rc-networkManagerClient.cpp/)

## References
 - Multiplayer Game Programming: Architecting Networked Games