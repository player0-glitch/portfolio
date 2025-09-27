---
title: Chat Room Server & Client
draft: false 
tags: [C++,TCP/IP, Networking, Sockets,Linux]
---
# Chat Room Server & Client in C++. What I Learned and Used 
>**GOAL** To build a complete multi-client chat room server and client in C++ using sockets, pthreads_t, demonstrating my understanding of networking concepts.

>**WHY** To show my hands-on experience with **low-level networking, non-blocking I/O**, and 
**multi-threading** (this joke wrote itself).

>**REPO** <a href="https://github.com/player0-glitch/chat-server.git" target="_blank" rel ="noopener noreferrer">Github</a>
---
## High-level Structure
- [Networking Fundamentals](#Networking-Fundamentals)
- [Server-side Implementation](#Server-side-Implementation)
- [Client-side Implementation](#Client-side-Implementation)
- [Skills Demonstrated](#Skills-Demonstrated)
- [Challenges Faced](#Challenges-Faced)
- [Future Implementations](#Future-Implementations)
- [Why This is Important](#Why-This-is-Important)

---

## Networking Fundamentals 
 

### TCP/IP Communications  
This type of server-client architecture uses the TCP/IP protocol. In short a 3-way handshake is used to establish a secure (well as secure as an amateur server connection should be) connection between the server and client. The client sends a SYN packet, then the server sends back a SYN_ACK to acknowledge that a client would like to create a socket connection . The client sends back an ACK packet to acknowledge that they have connected and therefore data transmission can occur,

As this is a pet project and not meant for production use, the server currently runs on the loopback IP (127.0.0.1 or 0.0.0.0) and a default port 10000. The server user can provide a custom port number through the command line arguments. The server is hard-coded with a backlog of 3. I won't explain this, as the reasons are 'lost to history' .

The code below shows the lifecycle of sockets, of course some bits and pieces including error handling will be omitted for brevity. 


```cpp
// Create a TCP socket with no additional settings/flags 
int server_fd=socket(AF_INET, SOCK_STREAM, 0);

//Setting the socket to be reusable (for when a client disconnects)
int opt=1;  //option that allows for the socket to be reusable
setsockopt(server_fd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt));

//bind the socket 
struct sockaddr_in server_addr={/*omitted code*/};
bind(server_fd, (const struct sockaddr *)&server_addr, sizeof(server_addr));

listen(server_fd, 3); //listen for incoming connections


while(1){
/* Big boy stuff happens here */
}
```

### POSIX Sockets API  
I make use of POSIX  sockets and the provided API, such as ```select()``` for monitoring multiple sockets without any busy waiting. This is the non-blocking I/O part of the server, allowing it to handle multiple clients efficiently (up to 1024 file descriptors which is more than enough for our context).

## Server-side Implementation

### Non-blocking I/O  
The server uses non-blocking sockets to handle multiple clients simultaneously without blocking the main thread. This allows the server to accept new connections while still processing existing ones.

### Client Queue  
The server maintains a queue of connected clients, allowing it to broadcast messages to all clients efficiently.

### Message Broadcasting 
The server broadcasts sends messages (packets) to all other clients in the chat who are successfully connected and have been added to the server's clients queue

## Client-side Implementation
Not only did I create my own communications protocol for clients, I also structured the client code to use separate threads for sending and receiving messages, ensuring that the client can handle incoming messages while still allowing the user to send messages without delay and almost no interruptions. This is taken advantage of in the non-blocking I/O server

### Threaded Architecture
  - **Sender Thread**: This thread is what is responsible for handling user texts from the terminal and sending the messages to the server. 
  A check is made for a symbol ```!q``` (this is how you exit vim) to check if the user wants to leave the chat room. This part of the messaging protocol.

```cpp
void *send_msg(void* fd){
  int client = *((int*) fd);
  std::string message(MSG_LEN, '\0');
  std::string dettach_symbol = "!q";
  while(1){
   //get user text input

   // check if user isn't trying to disconnect according to the protocol 

   ssize_t size=send(client,msg.c_str(),msg.length(),0);
   if(size<0){
     cerr<<"Failed to send data from terminal to server\n"<<endl;
  }
  return nullptr;
}
```

  - **Receiver Thread**: This thread does a little less work than the sender thread. It is responsible for listening in receiving messages from the server and displaying them out to the client. The buffer used in this thread is always reset after its contents have been shown to the client.
```cpp

void* receive_msg(void* fd){
  int client = *((int*) fd);
  char buffer[MSG_LEN+NAME_LEN];
  while(1){
    ssize_t size=read(client,buffer,sizeof(buffer));
    if(size<0){
      cerr<<"Failed to receive data from server\n"<<endl;
    }
    if(size==0){
      cout<<"Server has disconnected\n";
      break;
    }
    cout<<buffer<<endl;
  }
  return nullptr;
}
```

### User Registration 
When a client connects to the server, even before the client is able to join the chat-room, they send over their username to be registered using the messaging protocol.. The server then adds the client to the queue of connected clients and acknowledges the registration.

### Packet-Based Communication 
The client uses a simple packet-based communication system to send and receive messages. Each packet starts with a header indicating the packet type, followed by the message content.

## Skills Demonstrated 
#### Concurrecy:
Use of single-thread architecture with event-driven server design using  ```select()``` to handle file descriptors in a non-blocking manner, allowing the server to manage multiple clients .

#### Multi-threading:
Use of pthreads_t to handle sending and receiving messages on separate sockets.

```cpp 
pthread_t send_thread,receive_thread;
/*
 code here 
*/
//These functions are defined the client code
pthread_create(&send_thread, nullptr, send_msg, (void *)&socket_fd);
pthread_create(&receive_thread, nullpr, receive_msg, (void *)&socket_fd);

//These functions return nothing 
pthread_join(send_thread, nullptr); 
pthread_join(receive_thread, nullptr); 
```



#### Debugging and testing
I've made a fair use of the strace linux command to track down networking issues as I knew that at the time, my client-server architecture was poor. I also went out of my way to create a quick and dirty 
packet sniffers to get a view of the messages (packets) being sent over the network. This helped me understand the flow of data and identify further issues I would not have thought of on my had I not went out of my way.
## Challenges Faced
- **Disconnecting Clients**:  
The custom messaging protocol requires the users (clients) to use a symbol ```!q``` to signal to the server that they want to disconnect. This executes a 'graceful' 
exit (at least I'd like to believe so).

- **Queuing of Clients**:  
Created a simple queue 'system' (yes a data structure already exists) with a global counter to keep track of the number of clients connected. The queue is also able to 
shift clients closer to the 'front' of the queue when a client disconnects. This is a very small optimasation but it allows for all loops to not have to loop through the maximum client queue of 256.

- **Thread Termination**:  
I think I started pulling at my hair until I accepted that there's no need to over-engineer the code to exit a thread from within itself. I simply return from the thread function to terminate it and nothing breaks.  

## Future Implementations
- [ ] Encrypted Communication (TLS)  using nginx or openssl
- [ ] Private messaging between clients or simply put DMs

---

## Why This is Important
This project demonstrates my understanding of low-level networking concepts and my ability to implement both server and client side communication. If you look hard enough, you'll see that this project 
also showcases my understanding of multi-threading and concurrency.

### Contacts
email: [mokhaditlhalefo@gmail.com](mailto:mokhaditlhalefo@gmail.com)

LinkedIn: [linkedin.com/in/mokhaditlhalefo](https://www.linkedin.com/in/tlhalefo-mokhadi-445b05226/)

