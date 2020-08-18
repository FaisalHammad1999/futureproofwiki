One of the greatest benefits of the internet is the ability to communicate and share information and experiences! \
So far we have made apps that request information but sometimes we also want to receive information without explicitly asking.
Whilst we are not quite at 0 latency yet, there are some helpful tools that will allow us to open up direct channels of communication between different points.

### HTTP
Our bread and butter communication protocol
- Client makes a request
- Server responds with the requested data

### WebSockets
A WebSocket is a bi-directional communication channel between a client and a server. Once the WebSocket is open, the server can send messages to the client without being asked. 
- A client opens a WebSocket connection to the Server
- Client and Server can now send messages anytime whilst the connection is open
- Many clients can be connected to one server at a time
- A user could get a message to another user via the server and the receiving user would receive it directly without explicitly requesting eg. an API fetch
- Connection closes when requested (or on failure)

### WebRTC
WebRTC (Real Time Communication) is a relatively new project which can handle direct Client <=> Client data transfer without going via a Server after initial setup.
- Unless you are absolutely certain, there's a good chance you don't need this
- Potential for security risk is higher
- Potential for less latency (delay) in data transfer

***

## Socket.IO
Whilst [Socket.IO](https://socket.io/docs/) does use WebSockets for initial setup, it takes the WebSocket concept and wraps it up to be a bit easier to use and add some features.

### Server setup
- `npm init`
- `npm install express socket.io`
- `touch server.js`

Start with a basic server setup:
```js
const app = require("express")();
const server = require("http").createServer(app);
const io = require("socket.io")(server) // integrate our http server with a new instance of socket.io

// socket connection will go here

const port = process.env.PORT || 5001;
server.listen(port, () => console.log(`Express is running on port ${port}`))

server.listen(port, () => {
    console.log(`Open for play on port ${port}!`)
});
```

And then add your socket connection:
```js
io.on('connection', socket => {
    console.log("'Ello, who's this we got here?") // runs when client first connects

    socket.on("disconnect", socket => { // runs when client disconnects
        console.log("K bye then");
    });
});
```

### Client setup
Now to setup the client side. I am using a React app layout here but it is not required:
- `npm install socket.io-client`
```js
// in App.js
import io from "socket.io-client";
const serverEndpoint = "http://127.0.0.1:5001";

class App extends Component {
    state = { socket: null };

    componentDidMount(){
        const socket = io(deployedServer);
        this.setState({ socket });
    };

    componentWillUnmount(){
        this.state.socket.disconnect();
    };

    render(){
        return (
            <div id="App">Hi</div>
        );
    }
}
```

### Fire it up!
Now start up your socket server and then visit your client app
- Take a look at your server log and you should see "'Ello, who's this we got here?"!
Next, close your client.
- Take another look at your server log and you should see "K bye then"!

## Communicate back to the Clients
Let's send out some messages to our clients when someone new connects:
```js
// in server
io.on('connection', socket => {
    console.log("'Ello, who's this we got here?") // runs when client first connects

    // get total number of client connections
    const participantCount = io.engine.clientsCount

    // send event only to new connecting client
    socket.emit('admin-message', 'Hi there, new friend!')
    // send event to all other clients (not new connecting client)
    socket.broadcast.emit('admin-message', `A new friend has arrived!`)
    // send event to all clients
    io.emit('admin-message', `There is ${participantCount} x friend here now!`)


    socket.on("disconnect", socket => { // runs when client disconnects
        console.log("K bye then");
    });
});
```

All those messages come through as 'admin-message' events so let's listen out for events with that name on our client and log their data:
```js
// in App.js
componentDidMount(){
    const socket = io(deployedServer);
    socket.on('admin-message', msg => console.log(msg));
    this.setState({ socket });
};
```

## Add more functionality
From here you can go wild. The basic structure of any socket event is:
```js
// receiving:
socket.on('name-of-event', dataReceived => doThis())

// sending:
[who-to-send-to]('name-of-event', dataToSend)
```

So client - server - client flow for sending a message maybe:
```js
// in App.js
sendMessage = () => socket.emit('new-message', {username: 'Han', message: 'I know'});

// in server.js
socket.on('new-message', { username, message } => {
    socket.broadcast.emit('incoming-message', { username, message })
    socket.emit('admin-message', 'message sent')
})

// in App.js
socket.on('incoming-message', { username, message } => console.log(`${username} says: ${message}`))
```

## Multi-room
Socket.IO is great for handling multi-room functionality pretty seamlessly:
```js
// in App.js
joinRoom = () => this.state.socket.emit('request-join-game', { username: 'Leia', roomId: 'rebellion' });

// in server.js
socket.on('request-join-room', { username, roomId } => {
    // send event only to User
    socket.emit('entry-permission', { roomId })

    // send event to all other Users in specific room
    socket.to(gameId).emit('new-user-joining', { username, roomId })

    // send event to all Users in specific room
    io.in(gameId).emit('admin-message', `${inRoomCount} players now in ${roomId}!`)
})

// in App.js
componentDidMount(){
    const socket = io(deployedServer);
    socket.on('admin-message', msg => console.log(msg));
    socket.on('entry-permission', roomId => history.push(`/room/${roomId}`));
    socket.on('new-user-joining', ({ username, roomId })) => console.log(`${username} has joined ${roomId}`));
    this.setState({ socket });
};
```

To leave a room, send an event to the server that triggers your departure:
```js
// in server.js
    socket.on('leave-game', roomId => socket.leave(roomId);
```

## Nota Bene
- Don't send too much data in your socket events. If you hit a max callback issue, check what you are sending!
- Make sure to disconnect from rooms and socket connections when on departure!
- The [emit cheatsheet](https://socket.io/docs/emit-cheatsheet/) from the Socket.IO docs is invaluable!