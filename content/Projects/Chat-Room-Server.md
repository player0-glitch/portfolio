---
title: Chat Room Server & Client
draft: false 
tags: [C++,TCP, Networking, Sockets,Linux]
---
# Chat Room Server & Client in C++ - What I Learned and Used 
>**GOAL** To build a complete multi-client chat room server and client in C++ using sockets, pthreads_t, demonstrating my understanding of networking concepts.

>**WHY** To gain hands-on experience with **low-level networking, non-blocking I/O**, and 
**multi-threading** (this joke wrote itself).

>**REPO** [GitHub Repository](https://github.com/player0-glitch/chat-server.git)
---
## High-level Structure
- [Networking Fundamentals](#Networking-Fundamentals)
- [Server-side Implementation](#Server-side-Implementation)
- [Client-side Implementation](#Client-side-Implementation)
- [Skills Demonstrated](#Skills-Demonstrated)
- [Challenges Faced](#Challenges-Faced)
- [Future Implementations](#Future-Implementations)

---

## Networking Fundamentals 

**TCP/IP Communications**:

**POSIX Sockets API**:

## Server-side Implementation

**Non-blocking I/O**: The server uses non-blocking sockets to handle multiple clients simultaneously without blocking the main thread. This allows the server to accept new connections while still processing existing ones.

**Client Queue**: The server maintains a queue of connected clients, allowing it to broadcast messages to all clients efficiently.

**Message Broadcasting**:

## Client-side Implementation
Not only did it create my own communications protocol for clients, I also structured the client code to use separate threads for sending and receiving messages, ensuring that the client can handle incoming messages while still allowing the user to send messages without delay and almost no interruptions. This is taken advantage of in the non-blocking I/O server

**Threaded Architecture:**
  - **Sender Thread**:


  - **Receiver Thread**:


**User Registration** 

**Packet-Based Communication**: The client uses a simple packet-based communication system to send and receive messages. Each packet starts with a header indicating the packet type, followed by the message content.

## Skills Demonstrated 
#### Concurrecy:
Use of single-thread architecture with event-driven server design using  ```select()``` to handle file descriptors in a non-blocking manner, allowing the server to manage multiple clients .

#### Multi-threading:
Use of pthreads_t to handle sending and receiving messages on separate sockets.


## Challenges Faced

