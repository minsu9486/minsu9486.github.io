---
title: "roboCat_UserService.js"
permalink: /scripts/roboCat_UserService.js/
layout: splash
excerpt: "Robo Cat, Multiplayer Game"
---

***

~~~js
/**
 * A RESTful user service.
 * @module ./roboCat_UserService
 */

 /** required modules */
 var express = require('express');
 var mongoose = require('mongoose');
 var userData = require('./roboCat_UserData.js');  // to manage users' data

 /** code must listen on localhost (127.0.0.1), port 3100 */
 var port = process.env.EXPRESS_PORT;
 /** use Express */
 var app = express();
 /** use json parser in Express */
 app.use(express.json());
 
 /** UserData's startup-logic */
 userData.InitUserData();

 /** Creates a new user, succeeding if that user did not already exist. */
 app.post('/api/v1/users', function(req, res) {
 
  userData.CreateNewUser(req, res);
 });
 
 /** Tries to login with the given data. */
 app.post('/api/v1/login', function(req, res) {

  userData.LoginUser(req, res);
 });
 
 /** Retrieves the specified user by ID (the generated value). */
 app.get('/api/v1/users/:id', function(req, res) {
   
  userData.RetrieveUserData(req, res);
 });
 
/** Retrieves the specified user, searching by username. */
app.get('/api/v1/users?', function(req, res) {

  userData.SearchUserData(req, res);
 });

 /** Updates the specified user. Only the owner of the session may update itself */
 app.put('/api/v1/users/:id', function(req, res) {
   
  userData.UpdateUserData(req, res);
 });
 
 /** It returns the user information listed for the user associated. */
 app.post('/api/v1/connect', function(req, res) {

  userData.ConnectToGameServer(req, res);
 });

 /** server to listen port and function */
 let  server = app.listen(port, function() {
   var serHost = server.address().address
   var serPort = server.address().port
 });
 server.on('error', console.error.bind(console, 'Express Server connection error: '));

/** server to listen port and function */
 let db = mongoose.connection;
 db.on('error', console.error.bind(console, 'Mongoose DB connection error: '));
~~~