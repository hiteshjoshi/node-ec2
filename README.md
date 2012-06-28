# Node EC2 [![Build Status](https://secure.travis-ci.org/bigeasy/node-ec2.png?branch=master)](http://travis-ci.org/bigeasy/node-ec2)

Evended Node.js bindings to the EC2 Query API.

 * Home: http://bigeasy.github.com/node-ec2
 * Source: http://github.com/bigeasy/node-ec2
 * Documentation: http://github.com/bigeasy/node-ec2/wiki

 * Health: http://travis-ci.org/bigeasy/node-ec2

 * Discuss: http://groups.google.com/group/nodejs
 * Issues: http://github.com/bigeasy/node-ec2/issues

 * License: The MIT License

## Purpose

Launch Amazon EC2 instances from Node.js and execute callbacks when the
instances are available.

node-ec2 is a minimal Node.js API with absolutely no sugar. node-ec2 signs your
get requests and converts the XML responses to JSON. Becasue of this, there is a
one to one mapping between the Amazon Query API and the actions provided by
node-ec2 and changes to the Amazon Query API are available immediately to
node-ec2 applications.

You can learn more about node-ec2 at the node-ec2 GitHub web page and by reading
the wiki.

# Node EC2

Evended Node.js bindings to the EC2 Query API.

## Synopsis

Node EC2 is a thin layer over the Amazon Query API.

Because `node-ec2` is a thin layer over the Amazon Query API, you can use the
[Amazon API
Reference](http://docs.amazonwebservices.com/AWSEC2/latest/APIReference/index.html?query-apis.html)
to find your way around. Node EC2 calls translate directly to Amazon Query API.

Other than converting parameter maps to signed query strings and converting the
XML responses to JSON objects, there is no syntactic sugar. See Rationale.

Here's an example using
[RunInstances](http://docs.amazonwebservices.com/AWSEC2/latest/APIReference/index.html?ApiReference-query-RunInstances.html).

```
{ "key": "EXAMPLE"
, "secret": "EXAMPLE"
}
```

```javascript
// Require EC2.
var ec2 = require("ec2");

// Read in the configuration above.
var configuration = JSON.parse(fs.readFileSync("configuration.json", "utf8"));

// Create an ec2 function that uses your configuration.
ec2 = ec2(configuration)

// Run an instance and wait for it to become ready.
ec2("RunInstances", {
  ImageId: "ami-2d4aa444", KeyName: "launch_key"
}, running);


var reservationId, instanceId;
function running (error, response) {
  if (error) throw error;
  reservationId = response.reservationId
  instanceId = response.instancesSet[0].instanceId;
  ec2("DescribeInstances", starting);
}

function starting (error, response) {
  if (error) throw error;
  var reservation = response.reservationSet.filter(function (reservation) {
    return reservation.reservationId == reservationId;
  })[0];
  var instance = reservation.instancesSet.filter(function (instance) {
    return instance.instanceId == instanceId;
  })[0];
  if (instance.instanceState.name == "running") ready();
  else setTimeout(starting, 2500);
  if (instance.instanceState.name == "running") {
});

function ready () {
  console.log("Instance created with id: " + instanceId);
}
```

## Installing

The easiest way to install is using npm.

```
npm install ec2
```

You can also checkout the source code for using `git`. It has only one
dependency, the wonderful little XML parser `node-xml`.

## Reference

Node EC2 exports a function you can use to build an EC2 function.

```javascript
var ec2 = require("ec2");
```
function: createClient

Creates an {{AmazonEC2Client}} object for use in 

class: AmazonEC2Client

Communicates with the Amazon Query API.

function: call

  parameter: name

  The Amazon EC2 Query API action to perform.

  parameter: parameters optional

  Named parameters for the Amazon EC2 Query API action.

  parameter: callback   optional

  A callback function that is called with JSON object containing the Amazon EC2
  Query API repsonse when the Amazon EC2 Query API action completes.

Call an Amazon Query API action.

function: poll

  parameter: name

  The Amazon EC2 Query API action to perform.

  parameter: parameters optional

  Named parameters for the Amazon EC2 Query API action.

  parameter: callback   optional

  A callback function that is called with JSON object containing the Amazon EC2
  Query API repsonse when the Amazon EC2 Query API action completes. If the
  callback function returns `false` the query is performed again.

Call an Amazon Query API action while a condition is false.

function: execute

Run the query.

event: error

  parameter: error

  The error response structure.

  parameter: statusCode

  The HTTP status code for the error response.

Called when there is any error, both network errors, and exceptions thrown by
callbacks, so that you have a chance to release instances if things go wrong.


event: end

Called when all queries have completed successfully.
