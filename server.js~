var express = require('express');
var path = require('path');
var http = require('http');
var fs = require('fs');
var redis = require("redis");
var bodyParser = require('body-parser');
var request = require('request');
var client = redis.createClient();

var app = express();
var http = require('http').Server(app);
var i = 0;
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

app.use(express.static(path.join(__dirname, 'public')));
app.use(bodyParser());


//cors middleware
app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});

app.post('/clear', function(req, res, next) {
  console.log("clearing " + req.body.url + " from redis cache");
  /*client.del("/" + req.body.url, function(err, reply) {
  console.log(reply);
});
*/

  res.send("success");
});

//prerender middleware
app.use(require('prerender-node').set('prerenderServiceUrl', 'http://localhost:3000/').set('beforeRender', function(req, done) {
  client.get(req.url, done);
}).set('afterRender', function(err, req, prerender_res) {
  client.set(req.url, prerender_res.body);
}));

//final response from server
app.get('*', function(req, res) {
  console.log("herere" + req.url);
  i++;
  console.log("success--" + i);

  var filePath = path.join('views', 'index.ejs');
  var readStream = fs.readFileSync(filePath);

  res.writeHead(200, {
    'Content-Type': 'text/html'
  });

  // We replaced all the event handlers with a simple call to readStream.pipe()
  res.write(readStream);
  res.end();

});

http.listen(4000, function() {
  console.log('listening on *:4000');
});


module.exports = app;
