# Roomframework
Roomframework is an application framework for SPA(Single Page Application).  
The core feature is WebSocket wrapper.  
It is easy to combine with other frameworks.

## Features
- WebSocket(room.connection.js)
  - WebSocket wrapper
  - Manage reconnection
  - Manage authenticate token
  - Request handling like ajax
  - Register message handlers
- Cache(room.cache.js)
  - WebStorage wrapper
- Logger(room.logger.js)
  - WebSocketLogger

> To use WebSocket related features, you should use server side implementation for it.
> There is an implementation for Playframework.([roomframework-play](https://github.com/shunjikonishi/roomframework-play))

## Files
- src/room.*.js
- dist/room.*.min.js
- dist/roomframework[.min].js

All features are included in roomframework[.min].js.  
If you want to use WebSocket related features only, you can use room.connection[.min].js.

## Getting started
Create room.Connection instance with ws(s) URL.  
And send message by request method.

You can specify success and error callback like jQuery#ajax.  
Also, you can register callback by on method.

```javascript
var ws = new room.Connection("ws://room-sandbox.herokuapp.com/gettingstarted");
ws.on("chat", function(data) {
	console.log("EventHandler: " + data);
});
ws.request({
	command: "chat",
	data: "Hello world!",
	success: function(data) {
		console.log("Call and response: " + data);
	},
});
ws.request({
	command: "chat",
	data: "Hello world again!"
});
```

[room-sandbox.herokuapp.com](http://shunjikonishi.github.io/room-sandbox/) is an echo WebSocket server.  
You will get following result by running above script.

```
Call and response: Hello world!
EventHandler: Hello world again!
```

The response of first request is processed by success callback.  
The second response is processed by the event handler which was registered with 'on' method.  
Because the second request didn't specify success callback.

## Message structure
This framework defines send and receive message format.  

You don't need to aware of send message format. Because it is automatically generated by request method.

If you shall implement server side message, you must implement with following format.

#### Request message format
```
{
  "id" : number,       //Message number. Added by request method.
  "command" : string,  //Command name
  "data" : any json    //Any data
}
```
#### Response message format
```
{
  "id" : number,       //Message number of the corresponding request.(Optional)
  "command" : string,  //Command name
  "type" : string,     //json, html, text, or error. errot type is processed as error.
  "data" : any json    //Any data
}
```


## Constructor's option
The parameter of constructor is URL string of ws(s) protocol.  
Or, you can speicify the hash with other options.
Optional parameter name and its default values are follows.

|Paramter|Default|Description                           |
|:-------|:-------|:------------------------------|
|url|Required|URL of ws(s)|
|maxRetry|5|The number of retry, when disconnected.|
|retryInterval|1000|The interval of retry(ms). Retry interval is increased with this value.|
|logger|null|The object of output debug log. You can use console object.|
|authToken|null|If specified, this token is send by authCommand after connecting.|
|authCommand|room.auth|If authToken is specified, it is sent with this command name.|
|authError|null|The callback function for authCommand error.<br> Its parameter is response data.|
|onOpen|null|The callback function for WebSocket#onopen.<br> Its parameter is event object.|
|onClose|null|The callback function for WebSocket#onclose.<br> Its parameter is event object.|
|onMessage|null|The callback function for WebSocket#onmessage.<br> Its parameter is event object.|
|onSocketError|null|The callback function for WebSocket#onerror.<br> Its parameter is event object.|
|onRequest|null|The callback function for executing request method.<br> Its parameters are command name and data.|
|onServerError|null|The common callback function for error response.<br> Its parameter is response data.|

## Methods
### request(params): void
- params: object

Send a request.
Params can include followings.

|Key|Type|Description|
|:--|:--|:--|
|command|string|Command name. Required.|
|data|any|Sending data. Any Json.|
|success|function|The callback for success response.Its parameter is response data.|
|error|function|The callback for error response. Its parameter is response data.|

If you don't specify success or error, the response will be processed with an event handler that registered by on method.  
If event handler isn't registered, the error response will be processed by common error handler that registered by onServerError method.

### on(name, successFunc[, errorFunc]): this
- name: Command name
- successFunc: The callback for success message. Its parameter is response data.
- errorFunc: The callback for error message. Its parameter is response data. (Optional)

Register the event handlers for server messages.

### off(name): this
- name: Command name

Unregister the event handler.

### isConnected(): boolean
Return the WebSocket is connecting, or not.

### close(): void
Close WebSocket.

### sendNoop(interval[, sendIfHidden][, commandName]): int
- interval: Interval(ms)
- sendIfHidden: Send message when the browser is inactive. Default is false.
- commandName: Command name. Default is 'noop'.

Send a noop message on regular basis.  
You can cancel it by using clearInterval method with the return value of this method.

### onOpen(func): this
- func: Callback function. Its parameter is event object.

Specify the callback function for WebSocket#onopen.

### onClose(func): this
- func: Callback function. Its parameter is event object.

Specify the callback function for WebSocket#onclose.

### onMessage(func): this
- func: Callback function. Its parameter is event object.

Specify the callback function for WebSocket#onmessage.

### onSocketError(func): this
- func: Callback function. Its parameter is event object.

Specify the callback function for WebSocket#onerror.

### onRequest(func): this
- func: Callback function. Its parameters are command name and data.

Specify the callback function for invoking request method.

### onServerError(func): this
- func: Callback function. Its parameter is response data.

Specify the common callback function for the error response.  
If the error response is handled by request#error or on#error, this handler doesn't run.

# License
MIT

We have an commercial use version of this application officially supported.

Please contact us for details.

info@flect.co.jp
