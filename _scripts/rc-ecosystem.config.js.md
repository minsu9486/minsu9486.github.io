---
title: "ecosystem.config.js"
permalink: /scripts/ecosystem.config.js/
layout: splash
excerpt: "Robo Cat, Multiplayer Game"
---

***

~~~js
module.exports = {
  apps : [{
    name: 'roboCat_UserService',
    script: './roboCat_UserService.js',

    // Options reference: https://pm2.keymetrics.io/docs/usage/application-declaration/
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '1G',
    env: {
      // Main App
      EXPRESS_PORT : '3200',
      // Mongoose
      MONGO_SERVER_IP : '127.0.0.1',
      MONGO_SERVER_PORT : 27017,
      MONGO_DATADASE_NAME : 'RoboCat_UserData_V1',
      // Redis
      REDIS_SERVER_IP : '127.0.0.1',
      REDIS_SERVER_PORT : 6379,
      EXPIRATION_DURATION : 10, // in seconds
      // Game Server
      GAME_SERVER: '127.0.0.1:3300',
      SHARED_SECRET: 'HUSHHUSHITSSECRET'
    },
  }]
};
~~~