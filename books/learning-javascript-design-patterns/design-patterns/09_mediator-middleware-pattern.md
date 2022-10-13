# Mediator/Middleware Pattern

## What Is It?
The mediator pattern makes it possible for components to interact with each other through a central point: the mediator. Instead of directly talking to each other, the mediator receives the requests, and sends them forward! In JavaScript, the mediator is often nothing more than an object literal or a function.

e.g. An air traffic controller

## Example

### Chatroom
A chatroom that serves as the mediator betwen the users.

```js
class ChatRoom {
  logMessage(user, message) {
    const time = new Date();
    const sender = user.getName();

    console.log(`${time} [${sender}]: ${message}`);
  }
}

class User {
  constructor(name, chatroom) {
    this.name = name;
    this.chatroom = chatroom;
  }

  getName() {
    return this.name;
  }

  send(message) {
    this.chatroom.logMessage(this, message);
  }
}

const chatroom = new ChatRoom();

const user1 = new User("John Doe", chatroom);
const user2 = new User("Jane Doe", chatroom);

user1.send("Hi there!");
user2.send("Hey!");
```

### Real World Example
In Express.js, the `next` method calls the next callback in the request-response cycle. We'd effectively be creating a chain of middleware functions that sit between the request and the response, or vice versa.

```js
const app = require("express")();
const html = require("./data");

app.use(
  "/",
  (req, res, next) => {
    // First callback
	// Add header to request (hitting '/')
    req.headers["test-header"] = 1234;
    // Call next callback
    next();
  },
  (req, res, next) => {
    // Request now has header
    // This was added by previous middleware function
    console.log(`Request has test header: ${!!req.headers["test-header"]}`);
    next();
  }
);

app.get("/", (req, res) => {
  res.set("Content-Type", "text/html");
  res.send(Buffer.from(html));
});


app.listen(8080, function() {
  console.log("Server is running on 8080");
});
```

Every time the user hits a root endpoint `'/'`, the two middleware callbacks will be invoked.

