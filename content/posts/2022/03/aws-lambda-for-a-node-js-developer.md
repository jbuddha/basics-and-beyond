+++
author = "Reshma"
date = 2022-03-09T08:00:00Z
draft = true
lastmod = 2022-03-09T08:00:00Z
slug = ""
tags = []
title = "AWS Lambda for a Node JS Developer"

+++
Since its launch, AWS Lambda has quickly gained massive popularity for being an efficient server-less computing platform. The majority of its success can be mainly attributed to its ease to create and maintain server-less components using Lambda and the fact that it also supports many programming languages like JavaScript, Java, Python, C#, and many more. In this blog, I will show you how easy it is to create, maintain and run an API on AWS Lambda. Like the title says, we’re going to be using Node JS as our choice of programming language.

We’re going to be looking at the transition from a simple NodeJS API to an API running on AWS Lambda. Let’s keep the complexity low for now and pick a simple add API that’s going to take two numbers as a JSON and return a JSON that has the sum of those numbers. Let me break the entire process into bullet points, for your easy access.

* Creating an add API
* Invoking add API
* Pros & Cons of maintaining an API
* Creating an add function in AWS Lambda
* Creating a Test Event
* Using API Gateway to trigger function
* Monitoring invocations using Cloudwatch
* Pros & Cons of maintaining an API on AWS Lambda

**Creating an add API in local machine**

I’m assuming that you are familiar with some basic NodeJS concepts already. So, let’s begin with an empty folder and install the two dependencies we would need to create a simple API that accepts a JSON. Open your code editor of choice, I’m using Visual Studio Code. and type in the below command in the terminal to install express for creating rest servers and body-parser for handling JSON data types in request and response.

npm install -save-dev express body-parser

The installation is going to be pretty quick and once it's done let’s create an empty file and call it add-api.js by using this command.

    touch add-api.js

We’re going to be writing the server code for our rest API in this folder. Let’s import them first with require and create an express object and call it app. Let’s also set JSON as its body-parser. Next, we’ll create a constant port with a value 8081 which we’ll use to run our server later.

```js
var express = require('express'); 
var bodyParser = require('body-parser'); 
const { request, response } = require('express');

var app = express(); app.use(bodyParser.json());
const PORT = 8081;
```

We can now define the routing method [app.post](http://app.post) to handle all the post calls. Now, all we need to do is define a callback or a handler function inside this routing method that gets invoked when a client makes a post-call.

```js
app.post('/', (request, response) => 
         { let input = request.body; 
          let total = 0; 
          if (input.num1 && input.num2) 
            total = parseFloat(input.num1) + parseFloat(input.num2); 				
            response.json({sum: total}); 
         });
```

In the handler function above, we first read the input JSON from the request parameter’s body. We have to return a total, we instantiated a variable with 0. Then, we read the parameters num1 and num2 of the input JSON.

In the If statement, we converted each of them to a number using the parse float method and added them to set the value of the total variable. Once the total is calculated, we returned it as a JSON using the response.json method. We set the key as a sum and assigned total to it to satisfy the requirements we have set for ourselves.

However, we are not done yet. We still have to ask NodeJS to listen to port 8081. This can be achieved by invoking app.listen method and passing the port number as the argument. While we are at it, let us add a log statement to let the user know that the server is ready.

```js
app.listen(PORT); 
console.log(`Listening on <http://localhost>:${PORT}`);
```

This completes our first task. We have successfully created an API that adds two numbers and returns their sum. Now, let’s move on to our next task at hand.

**Invoking add API in local machine**

Running the above function is quite easy. Just run the command `node add-api.js` and wait for the message that says the server is listening on the given port. We can now invoke the server’s POST handler using any REST client. I’m going to use the ThunderClient extension of VSCode to invoke API, you may use Postman or some other client.

Click on the New Request button and change the URL [localhost](http://localhost) 8081. The HTTPS must be changed to HTTP. POST must be selected in the method dropdown and switch to the Body tab to pass the input JSON.

Update the JSON content to pass num1 and num2 parameters, like shown in the image above, and click Send button to invoke the API. If we have done everything right, the result must be visible on the response tab of the client. Try out different numbers and the sum should be accurate. If there are any errors, we should see appropriate error messages. Of course, we can clean it up and handle errors properly, however as we are not really planning to publish this API to anyone, let us not worry about that.

One key point I would like you to understand is that we will get the responses as long as the server is running. If the server is closed, there is no one listening on port 8081 and we get the error “connection was refused” by the server.

This brings up a key differentiator for server-less computing. Whether there are any clients to our server or not, our server should always be up and running. Even though the API handler is invoked based on requests, making it an event-driven implementation, this only works as long as the server is up and is not busy handling some other clients.

Let’s recap what we have done with our NodeJS implementation. We have created a REST API that accepts a JSON with two parameters num1 and num2 and adds those numbers to return the sum as JSON.

Let’s take a step back and understand what’s going on here. We have a server which is running on a computer. In the same computer, we have a client, which is Thunderclient in our example, which is accessing our REST API running on the same server, hence [localhost](http://localhost), and it just knows that it needs to hit the port 8081.

But what if this is not what we want? What if the configuration that we want is somewhat like this: The server or REST API is in a different computer and the client which is trying to access that particular API is in a different computer. Now, the localhost 8081 no longer works because, for Thunderclient, localhost means the computer in which the Thunderclient is running. We can fix this issue with some domain magic and change the localhost:8081 to something like [www.basicsandbeyond.dev](http://www.basicsandbeyond.dev) and allow the users to access it from the internet.

So, whenever Thunderclient has to send a JSON request, it sends the request to the ISP, from where it travels through all the internet to reach the host and invoke the API. The response follows the same route. This system works perfectly if the two computers, the server, and the client, are close by. For example, if the server is in Seattle and the client is maybe in, say, Atlanta. The setup also works perfectly fine if the user is trying to access the server from a completely different point like a mobile phone, even if the mobile phone is in a different part of the world.

As more and more clients try to access our server, we have no choice but to keep increasing its size but there comes a point where there are just too many requests, and our server now just burns and crashes. We do not want that. Here’s where AWS Lambda comes into play. Being an efficient server-less computing platform, it takes away our burden to maintain the servers ourselves by managing all the requests coming via lambda functions. To take a practical look at its functionality, let’s create and execute the same node.js function using AWS Lamda.