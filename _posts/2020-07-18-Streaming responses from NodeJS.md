---
layout: post
title: "Streaming with  NodeJS and oboe.js"
date: 2020-07-18
excerpt: "Streams - NodeJS"
image: "/images/streams.jpg"
---

## Streaming responses with NodeJS and capturing them with oboe

Have you ever had to build a page which shows abundant amount of data which depends on N number of upstream services. Let's say you are building a Users list page which displays users from different countries and each country has it's own upstream service serving the data .

For example, 1. UK users come from the UK users service 2. HU users come from the HU users service 3. IN users come from the IN users service

… and the list can go on .

There are a number of ways you can solve this conventionally

    1. Make a single API call from your frontend client ( called /users maybe ? ) to an orchestrator / BFFE server which takes care of calling all these services , aggregating the data and sending it to the client. This would mean you keep your front end in the transient state until all the data has loaded. The time to first contentful paint will be nearly  be equal to the slowest API call from the Backend
    2. Make multiple API calls from the frontend client instead of doing it from the orchestrator . This way you can progressively render the users table as and when you start receiving data from the backend services. This is a good way to solve the problem and can be used for simple usecases where you don’t have many API calls to make. But if you have loads of data to load , it can have its shortcomings when it comes to the limitation of the browser of making multiple API calls to the same domain ( read Domain sharding ) .

One other solution to this problem which this post focusses on makes use of the best parts of both the methods described above . This solution is Streaming .

With streaming, we can still make a single API call to a backend ( like method 1 ) and continue progressively rendering the table as and when each service responds ( like method 2 )

Let's start with writing dummy endpoint in NODEJS which our frontend client will call. The endpoint will gather data from 2 services ( our dummy promises in this case ) and stream the response of each service to the client. We are going to use express middleware and the trick here is to use res.write to send chunked response to the client

Add the following to your express JS code

```
const getUKUsers = (res) => {
const ukUsers = {
ukUsers: [
{
id: 1,
name: "John",
age: 24,
},
{
id: 2,
name: "Jeffrey",
age: 56,
],
};
return new Promise((resolve, _) => {
setTimeout(() => {
res.write(JSON.stringify(ukUsers));
resolve();
}, 0);
});
};
const getHUUsers = (res) => {
const huUsers = {
huUsers: [
{
id: 1,
name: "Jake",
age: 34,
},
{
id: 2,
name: "Jonathan",
age: 67,
},
],
};
return new Promise((resolve, _) => {
setTimeout(() => {
res.write(JSON.stringify(huUsers));
resolve();
}, 5000);
});
};
app.get("/api/users", (req, res) => {
Promise.all([getUKUsers(res), getHUUsers(res)]).then(() => {
res.end();
});
});
```

On your client, add this code in your data fetch trigger point ( useEffect in my example )
const oboe = require("oboe");

```
useEffect(() => {
oboe("/api/users")
// .node("*", function(all) {
// console.log("all", all);
// })
.node("ukUsers.*", function(ukUsers) {
// This callback will be called everytime a new object is
// found in the ukUsers array.
console.log("Uk user name", ukUsers.name);

})
.node("huUsers.*", function(huUsers) {
console.log("Hu user name", huUsers.name);
})
.done(function(users) {
console.log("users", users);
// console.log(
// "there are",
// users.ukUsers.length,
// "UK users",
// "and",
// users.huUsers.length,
// "HU users"
// );
})
.fail(function(err) {
console.log("err", err);
// we don't got it
});
}, []);
```

When you try out the above code example, you will see the following sequence of logs in the console.log

    1. UK Users list immediately since the getUKUsers promise on the server responds immediately
    2. HU users list after 5 seconds since getHUUsers promise on the server responds after 5 seconds

Given this , you can immediately render the uk users list and the browser can paint your UI even though one of the services in this case has not returned data .

Please note, this is just one of the way of gathering data and NOT the only / best way to do it . There are tradeoffs with every approach and this one is no different . It's however helpful in cases where there's huge chunks of data to be fetched and streamed to the client
