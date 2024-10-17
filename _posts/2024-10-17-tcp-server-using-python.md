---
layout: post
title: "TCP Server using Python"
categories: Python
author: "Marcel van Workum"
---

I recently started working through the [CodeCrafters](https://codecrafters.io/) challenges. I have found them to be a fun and interactive way to learn fundamental concepts and technologies.

Whilst working through these challenges a common task is to create a TCP server. To do so in python is quite simple.

```python
import socket

def main():
    host = "localhost"
    port = 9092

    # Create a TCP server using the socket module.
    # We set the reuse_port option to False. 
    # This will mean that if we try and bind to the same port before the previous connection has been closed, we will get an error.
    # I have found this to be useful for debugging purposes.
    server = socket.create_server((host, port), reuse_port=False)

    # Start listening for incoming connections
    while True:
        # First we accept the connection. This will block until a connection is made.
        # Once a TCP connection is made, we will get a new socket object and the address of the client.
        conn, addr = server.accept()
        print(f"Connection from {addr}")

        # Rather than explicitly closing the connection, we use a context manager.
        # This will ensure that the connection is closed when we are done with it.
        # Regardless of whether an exception is raised or not.
        with conn:
            CHUNK_SIZE = 1024
            while True:
                # The data is received in chunks.
                # so we continually read the chunks until there is no more data.
                data = conn.recv(CHUNK_SIZE)
                if not data:
                    break

                print(f"Received {data}")

                # For each chunk of data we receive, we send it back to the client.
                # This essentially creates an echo server.
                conn.sendall(data)


if __name__ == "__main__":
    main()

```

Now, if you run this script and connect to it using a TCP client, you will see that it will echo back any data you send to it.

```bash
$ nc localhost 9092
Hello World
Hello World
```
