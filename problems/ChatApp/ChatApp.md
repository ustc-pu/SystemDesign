## Overall

Client send message, Chat Server store and forward the message to the receiver.

Client/sender could use a HTTP connection to send messages to the server.
But how does the server send messages back to the receiver is difficult.

### Polling
Receiver will periodically send request to the server and ask if there is new information ready. Polling could be very costly and overloads the server. Usually not a good idea.

### Long Polling

Receiver will make the conncetion open until either server sends back a message or the conenction is timed out. If the receiver receives a message, it will make a new conneciton immediately.

If a user does not chat much, long polling still makes periodic connections after timeouts.

### Websocket
Initiated by the client, true bidirectional. WebSocket starts as an HTTP connection and then upgrades to a WebSocket connection through a handshake process. 
