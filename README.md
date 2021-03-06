#Commotion Websocket Messaging Service (Meshaging)

For imformation on Commotion the mesh networking system this project runs on see http://commotionwireless.net/

A websocket server that allows routing of messages between clients connected to multiple
commotion access points nodes. It comes with a small js library that building on top of the html5 WebSocket object and implements the protocol specifics. Allows for a simple API with a subscriber based messaging system similar to socket.io allowing developers to easily build apps on the system.

The js library and backend server allow for developers to easily create applications that leverage
Commotion's mesh network and existing browser technology. With this its possible for a developer 
to create a Commotion app that communicates with clients on the "mesh" 100% based in the clients browser.

```javascript
// Specifiy the websocket server
var ws_server = "ws://"+window.location.hostname+":7681";

$(document).ready(function(){
    
    /**
     * Create the CommotionSocket object with the websocket server and
     * a list of applications to announce to the network.
     */
    var cws = new CommotionSocket(ws_server,["chat-server","other-app"],function(){
        // On connection
        
        setTimeout(function(){
            
            // Example of sending "Hello World" to each client
            cws.forclients(function(client){
                // Send must take the libraries address format which is
                // { 
                //    ip:// 32bit number representaion of ip address, host order
                //    id:// unique id for ip, allows multiple clients with the same ip. 
                // }
                cws.send(client.address,"norm","Hello World");
            });
            
            // Example of brodcasting "Hello World Broadcast" to each client
            cws.broadcast("norm","Hello World Broadcast");

        },2500);

    });
    
    /**
     * Subscribe to specific messages this will allow js to callback only messages
     * that application is listening for.
     */
    // Handle messages "norm" and "error" 
    cws.on(["norm","error"],function(ret){
        // Callback for message
    })
    
    cws.onclose(function(){
        // On dissconnect
    });
    
    cws.ontopologychange(function(ret){
       // Everytime the topology updates
       
        // Use for clients to loop through all clients connected.
        cws.forclients(function(client){
            // Handle each client
        });
    });

```

##Currently Supports
* Sending messages to individual clients subscribe (unicast)
* Message subscribing
* Automatic topology updates
* Topology management in memory 

##Roadmap
* Access point (ap) to ap routing of messages
* js browser storage system for apps and distributed storage system
* client display name creation system
* allow disconnect method for signing off
* timestamp on message, could be done in js or backend


## Dependencies for JS lib
* jquery

## Dependencies for Server
* [libwebsockets](http://git.warmcat.com/cgi-bin/cgit/libwebsockets/) : C Websocket server
* [libjanson](http://www.digip.org/jansson/) : C implementation of json.

##Setting up test server
1. Checkout source for git
2. run `./configure`
3. run `make`
4. run './src/wsroutingserver' to start server
5. `cd tests/node-server/` and run `node server.js` to start test node html server to serve js and html


##Sample Luci Application
The package in Commotion installs a sample luci application and js files to the luci-static and lua controller folders.

To enable app in router add the following lines to `commotionfeed/luci-commotion/luasrc/controller/commotion/commotion.lua`

```
 entry({"commotion", "apps"}, alias("commotion", "apps", "apps"), i18n("Applications"), 10).index = true
 entry({"commotion", "apps", "chatapp"}, template("commotion/chatapp"), i18n("Chat Application"), 91)
```

###Files Installed
* /www/luci-static/resources/commotion-ws.js - JS Lib
* /www/luci-static/resources/jquery.min.js - Jquery for JS lib





For more documentation on the server see readme in src
