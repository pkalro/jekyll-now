---
layout: post
title:  "Delving into Microfrontends - Chapter I"
date:  2020-01-10
excerpt: "Microfrontends - A chapter wise breakdown"
image: "/images/2018-06-06_promiseStates.png"
---

## Chapter I - Entering the Microfrontends world

A new fancy term has started doing the rounds in the industry . Is it a framework ? A technique. ? A process ?  This post is the first in a series  dedicated to dwelling into the basics of what Microfrontends signifies and a simple example of getting it up and running. 

We have all heard of Microservices being a popular architectural choice for the services world. It allows isolating complexity and enables autonomous teams handling domain specific responsibilites . A classic example is Uber , which popularized the microservices architecture style with it's segregation of services each for location management , session management, ride management , etc.

A practical / simple microservices architecture would look like this

[insert diagram]


This means that though the services have segregated responsibility with their own paramaters for handling scale and load, the client or the frontend is still sitting on a monolith which involves combining data from all the microservices and showing it to the user. Which also means that business logic cutting across complex domains, needs to be handled by the same frontend monolith . 

While this has been the status quo , and there have been some design principles which have certainly made things simpler, isolating complexity on the frontend was still unachieveable. However, there seems to be a new architectural style emerging solely for the frontend / clients to solve exactly this problem . Enter Microfrontends !

Microfrontends does  for the frontend , what Microservices does for the backend. Yes that's how simple it really is. 
Microfrontends is a means to to run multiple applications on the same webpage . Redefening the previous image to fit in Micrfrontends would make it look something like this.

[insert diagram]

As you can see, while Microservices enabled our backend to be split horizontally, our frontend was still a single block in the diagram. With Microfrontends, the frontend has been split with the same boundaries as the backend [while this might not be practically necessary with Backend for Frontend ( BFF ) design ]. 

The first question that comes to our mind is how these Microfrontends communicate and coexist on the same page. This again can be mapped to corresponding communication pattern followed by Microservices .

Microservices communicate among themselves through APIs . An API is an interface provided by a service / function / or any running prpgram which allows outside services / functions to access it.
Similarly, Microfrontends communicate amongst themselves using APIs . Now you might ask , in Microservices , the APIs are exposed over a communication channel ( or via URI ) and mostly ( not always ) accessed over http, but what is the communication channel with Microfrontends, since the frontends already coexist on the same webpage . Well , there are many answers to this . My personal favourite is the JS `window` object. 

More about the window object in Part 2 when we talk about a practical example of rendering multiple micro-apps on the same webpage. 

This post was mostly intended as a starter . For the main course, please wait for Part 2 :D





 

