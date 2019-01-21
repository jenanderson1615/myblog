---
title: Basic node.js Server
date: '2019-01-21T11:00:00.000Z'
---
I've been working on making a node.js server for awhile.  The decision to work on a node.js server stems from wanting to know more about becoming a full stack web developer and wanting to make complete projects that seem interesting to me.  The server at my work is using Swift and Vapor to make the endpoints, but node.js seems to be a more universal choice for making a web server, as front end web developers already know javascript.  

I decided to make a simple website to serve up a bucket list of some of my travel goals.  The front end will be simple: a list of the places I want to go that are links to find out more information.  I intend to add add/edit/delete functionality as well, but for now I am just working on a 2 GET requests.  I have a simple MySql database I set up to test my requests.  There are 3 tables - location_info, location_links, and location_reasons.  The links and reasons tables have foreign key ties to the location_info table.  

I want to make 2 GET requests - GET locations, which should be return all the locations in location_info and GET location_details/:location_id, which returns the location name with a dictionaries of reasons and links.  The front end will then make a list of the locations with links to find out more info on the details page.  

I made my server with Node.js, Express, body-parser and mysql.  The first thing I do is create a mysql connection to my database using mysql.createConnection();  Then, I use express to create a /locations route. 
Most of this is from blog posts on how to make server routes in express.  I do a simple query to get all the locations in the location_info file and use body parser to parse the results and return a dictionary.  Running the server allows me to go to http://localhost:9009/locations and get a list of the locations.  See below for the whole file creating the /locations endpoint.

```
var express = require("express");
var app = express();
var bodyParser = require("body-parser");
var mysql = require("mysql");

//database connection
var connection = mysql.createConnection({
    host: "localhost",
    user: "root",
    password: "",
    database: "locations"
});

connection.connect(function (err) {
    if (err) {
        console.error('error connecting: ' + err.stack);
        return;
    }
});

var port = process.env.por || 9009;

app.use(bodyParser.json());

app.route('/locations')
    .get(function (req, res) {
        connection.query("select location_name from location_info", function (err, result) {
            if (err) throw err;
            res.send(JSON.stringify({
                "status": 200,
                "error": null,
                "response": result
            }));
        });
    });

app.listen(port, function () {
    console.log("Express server running on port %d", port);
});
```
