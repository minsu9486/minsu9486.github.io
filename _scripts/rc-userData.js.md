---
title: "roboCat_UserData.js"
permalink: /scripts/roboCat_UserData.js/
layout: splash
excerpt: "Robo Cat, Multiplayer Game"
---

***

~~~js
/**
 * User data management module.
 * @module ./roboCat_UserData
 */

/** required modules */
const base64url = require('base64url'); // to encode ID
const mongoose = require('mongoose');   // general user data will be stored
const redis = require('redis');         // session will be stored
const crypto = require('crypto');       // to use sha256

// Connect Mongoose according to the given env values
mongoose.connect('mongodb://' + process.env.MONGO_SERVER_IP + ':' + process.env.MONGO_SERVER_PORT + '/' + process.env.MONGO_DATADASE_NAME, {
    useNewUrlParser : true,
    useCreateIndex: true,
    useUnifiedTopology : true,
    useFindAndModify : false,
});

// Create Redis according to the given env values
let redisClient = redis.createClient(process.env.REDIS_SERVER_PORT, process.env.REDIS_SERVER_IP);

// Mongoose Schema
let userDataSchema = new mongoose.Schema({
    id : String,
    username : String,
    password: String,
    avatar: String,
    token : String
});
let UserData = mongoose.model('userData', userDataSchema);

// Error check for Redis
redisClient.on("error", function (err) {
    console.log("Error " + err);
});

 /** functions for data management */
module.exports = {
    /** Initialize any data for execute any startup-logic */
    InitUserData:function() {
    },

    BuildToken:function(message) {
        let hash = crypto.createHash('sha256');
        hash.update(message);
        let token = hash.digest('base64');
        return token;
    },

    /** Create a new user on the data */
    CreateNewUser:function(req, res) {

        var username = req.body.username;
        var password = req.body.password;
        var avatar = req.body.avatar;

        UserData.findOne(
            {username: username},
            (err, model) => {
                if (err != null) {
                    console.log(err);
                    res.sendStatus(500);
                    return;
                }
                // found the same username, can't create
                if(model != null) {
                    res.status(409).send({error: "duplicated username"});
                    return;
                }
                // the given username is unique, need to create
                else {
                    let id = base64url(username);

                    UserData.findOneAndUpdate(
                        {username: username},
                        {$set : 
                            {
                                password: password,
                                avatar: avatar,
                                id: id
                            }
                        },
                        {
                            new : true, 
                            upsert : true 
                        }, 
                        (err, model) => {
                            // 
                            if (err != null) {
                                console.log(err);
                                res.sendStatus(500); 
                                return;
                            }
                            else{
                                res.json({
                                    "id": id,
                                    "username": username,
                                    "password": password,
                                    "avatar": avatar
                                });
                            }
                        });
                }

            });
    },

    LoginUser:function(req, res) {

        var username = req.body.username;
        var password = req.body.password;

        var query = UserData.findOne(
            {username: username},
            (err, model) => {
                if (err != null) {
                    console.log(err);
                    res.sendStatus(500); 
                    return;
                }

                // no username in the data
                if(model == null) {
                    res.status(400).send({error: "bad username"});
                    return;
                }
                // found the username, need to check password
                else {
                    // wrong password
                    if(model.password != password) {
                        res.status(403).send({error: "bad password"});
                        return;
                    }
                    // password is matched
                    else {
                        var session = base64url(username);
                        
                        var date = new Date(); // to generate a random number based on the time
                        var token = this.BuildToken((Math.random() * date.getTime()).toString());
                      
                        this.UpdateAuthentication(username, session, token);
                  
                        res.json({
                          "session": session,
                          "token": token
                        });
                    }
                }
            });

    },

     /** Retrieves the specified user by ID (the generated value). */
    async RetrieveUserData(req, res) {

        var didSendData = false;
        var redisDataBySession = undefined;
        var session = req.body.session;
        var token = req.body.token;

        // 401: no session
        if(session == undefined) {
            res.status(401).send({error: "no session"});
            return;
        }

        try {
        // 401: bad session
        await redisClient.hgetall(session, (err, redisData) => {
            if(didSendData) return;
            if (err != null) {
                console.log(err);
                res.sendStatus(500);
                didSendData = true;
                return;
            }

            if(redisData == null) {
                res.status(401).send({error: "bad session"});
                didSendData = true;
                return;
            }

            redisDataBySession = redisData;
        });
        } catch(e) {
            console.log('// 401: bad session: ' + e);
        }


        try {
        // 401: bad token
        await UserData.findOne({token: token}, (err, model) => {
            if(didSendData) return;
                if (err != null) {
                    console.log(err);
                    res.sendStatus(500);
                    didSendData = true;
                    return;
                }

                if(model == null) {
                    res.status(401).send({error: "bad token"});
                    didSendData = true;
                    return;
                }

                // 404: bad ID
                UserData.findOne({id: req.params.id}, (err, model) => {
                    if(didSendData) return;
                        if (err != null) {
                            console.log(err);
                            res.sendStatus(500);
                            didSendData = true;
                            return;
                        }
        
                        if(model == null) {
                            res.status(404).send({error: "bad ID"});
                            didSendData = true;
                            return;
                        }
        
                        // session is verified, gives with password info
                        if(model.username == redisDataBySession.username) {
                            res.json({
                                "id": model.id,
                                "username": model.username,
                                "password": model.password,
                                "avatar": model.avatar,
                            });
                        }
                        // session is different, no password info
                        else {
                            res.json({
                                "id": model.id,
                                "username": model.username,
                                "avatar": model.avatar,
                            });
                        }
                    });
            });
        } catch(e) {
            console.log('// 401: bad token' + e);
        }
    },

    /** Retrieves the specified user, searching by username. */
    async SearchUserData(req, res) {

        var didSendData = false;
        var redisDataBySession = undefined;
        var session = req.body.session;
        var token = req.body.token;

        // 401: no session
        if(session == undefined) {
            res.status(401).send({error: "no session"});
            return;
        }

        try {
        // 401: bad session
        await redisClient.hgetall(session, (err, redisData) => {
            if(didSendData) return;
            if (err != null) {
                console.log(err);
                res.sendStatus(500);
                didSendData = true;
                return;
            }

            if(redisData == null) {
                res.status(401).send({error: "bad session"});
                didSendData = true;
                return;
            }

            redisDataBySession = redisData;
        });
        } catch(e) {
            console.log('// 401: bad session: ' + e);
        }

        try {
            // 401: bad token
            await UserData.findOne({token: token}, (err, model) => {
                if(didSendData) return;
                    if (err != null) {
                        console.log(err);
                        res.sendStatus(500);
                        didSendData = true;
                        return;
                    }
    
                    if(model == null) {
                        res.status(401).send({error: "bad token"});
                        didSendData = true;
                        return;
                    }

                    
                    // 404: bad Username
                    UserData.findOne({username: req.query.username}, (err, model) => {
                        if(didSendData) return;
            
                        // 400: no Username
                        if(req.query.username == undefined) {
                            res.status(400).send({error: "no Username"});
                            didSendData = true;
                            return;
                        }
            
                            if (err != null) {
                                console.log(err);
                                res.sendStatus(500);
                                didSendData = true;
                                return;
                            }
            
                            if(model == null) {
                                res.status(404).send({error: "bad Username"});
                                didSendData = true;
                                return;
                            }
            
                            // session is verified, gives with password info
                            if(model.username == redisDataBySession.username) {
                                res.json({
                                    "id": model.id,
                                    "username": model.username,
                                    "password": model.password,
                                    "avatar": model.avatar,
                                });
                            }
                            // session is different, no password info
                            else {
                                res.json({
                                    "id": model.id,
                                    "username": model.username,
                                    "avatar": model.avatar,
                                });
                            }
                        });
                });
        } catch(e) {
                console.log('// 401: bad token :' + e);
        }
    },

    /** Update the given user to the given info */
    async UpdateUserData(req, res) {

        var didSendData = false;
        var redisDataBySession = undefined;
        var session = req.body.session;
        var token = req.body.token;

        // 401: no session
        if(session == undefined) {
            res.status(401).send({error: "no session"});
            return;
        }

        try {
            // 401: bad session
            await redisClient.hgetall(session, (err, redisData) => {
                if(didSendData) return;
                if (err != null) {
                    console.log(err);
                    res.sendStatus(500);
                    didSendData = true;
                    return;
                }
                
                if(redisData == null) {
                    res.status(401).send({error: "bad session"});
                    didSendData = true;
                    return;
                }

                redisDataBySession = redisData;
            });
        } catch(e) {
            console.log('// 401: bad session: ' + e);
        }

        try {
            // 401: bad token
            await UserData.findOne({token: token}, (err, model) => {
                if(didSendData) return;
                    if (err != null) {
                        console.log(err);
                        res.sendStatus(500);
                        didSendData = true;
                        return;
                    }
    
                    if(model == null) {
                        res.status(401).send({error: "bad token"});
                        didSendData = true;
                        return;
                    }
                });
        } catch(e) {
                console.log('// 401: bad token :' + e);
        }

        try {
            await UserData.findOne({id: req.params.id}, (err, model) => {
                if(didSendData) return;

                if(model == null) {
                    res.status(404).send({error: "No ID"});
                    didSendData = true;
                    return;
                }

                if(model.username != redisDataBySession.username) {
                    res.status(403).send({error: "Bad user"});
                    didSendData = true;
                    return;
                }

                UserData.findOneAndUpdate(
                    {id: req.params.id},
                    {$set : 
                        {
                            username: req.body.username,
                            password: req.body.password,
                            avatar: req.body.avatar
                        }
                    },
                    {
                        new : true, 
                        upsert : true 
                    }, 
                    (err, model) => {
                        if(didSendData) return;
                        if (err != null) {
                            console.log(err);
                            res.sendStatus(500);
                            didSendData = true;
                            return;
                        }
    
                        // 404: Bad ID
                        if(model == null) {
                            res.status(404).send({error: "Bad ID"});
                            didSendData = true;
                            return;
                        }
                        // 200: Success
                        else {
                            res.json({
                                "id": req.params.id,
                                "username": req.body.username,
                                "password": req.body.password,
                                "avatar": req.body.avatar,
                            });
    
                            redisClient.hmset(session, {username:req.body.username});
                            redisClient.expire(session, process.env.EXPIRATION_DURATION);
    
                            didSendData = true;
                            return;
                        }
                    }
                );
            });
        } catch(e) {
            console.log('// 403: Bad user :' + e);
        }
    },

    async ConnectToGameServer(req, res)
    {
        var session = req.body.session;
        var token = req.body.token;
        var didSendData = false;

        // 401: no session
        if(session == undefined) {
            res.status(401).send({error: "no session"});
            return;
        }

        try {
            // 401: bad session
            await redisClient.hgetall(session, (err, redisData) => {
                if(didSendData) return;
                if (err != null) {
                    console.log(err);
                    res.sendStatus(500);
                    didSendData = true;
                    return;
                }
                
                if(redisData == null) {
                    res.status(401).send({error: "bad session"});
                    didSendData = true;
                    return;
                }
            });
        } catch(e) {
            console.log('// 401: bad session: ' + e);
        }

        try {
            // 401: bad token
            await UserData.findOne({token: token}, (err, model) => {
                if(didSendData) return;
                    if (err != null) {
                        console.log(err);
                        res.sendStatus(500);
                        didSendData = true;
                        return;
                    }
    
                    if(model == null) {
                        res.status(401).send({error: "bad token"});
                        didSendData = true;
                        return;
                    }

                    var server = process.env.GAME_SERVER;
                    // to make the Connect token unique and disposable
                    var tokenConnect = this.BuildToken(model.username + model.avatar + process.env.SHARED_SECRET);
 
                    res.json({
                      "id": model.id,
                      "username": model.username,
                      "avatar": model.avatar,
                      "server": server,
                      "token": tokenConnect
                    });

                    didSendData = true;
                    return;
                });
        } catch(e) {
                console.log('// 401: bad token :' + e);
        }
    },

    /** Update session and token and assign to the given user */
    UpdateAuthentication:function(username, session, token) {

        UserData.findOneAndUpdate(
            {username: username},
            {$set : 
                {
                    token: token
                }
            },
            {
                new : true, 
                upsert : true 
            }, 
            (err, model) => {
                if (err != null) {
                    console.log(err);
                    res.sendStatus(500);
                    return;
                }
            });

        redisClient.hmset(session, {username:username});
        redisClient.expire(session, process.env.EXPIRATION_DURATION);
    },
 };
~~~