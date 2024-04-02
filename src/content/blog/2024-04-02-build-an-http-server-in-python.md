---
title: Build an HTTP server in Python
pubDatetime: 2024-04-02T13:44:00-07:00
postSlug: build-an-http-server-in-python
featured: true
draft: false
tags:
  - project
  - Python
description: I built an HTTP server in Python.
---
As the title suggests, I built an HTTP server using Python. I followed this project on [Codecrafters](https://app.codecrafters.io)， which is a great platform for implementing things on your own. Below are the specific steps taken to build a basic server.

## Bind to a port

We use `socket` to spin up a server.

```python
server_socket = socket.create_server(("localhost", 4221), reuse_port=True)

server_socket.accept() # wait for client
```

## Respond with 200

For any request, respond with `HTTP/1.1 200 OK\r\n\r\n`, which is the **HTTP status line**. `\r\n` is **CRLF**, first one is the end of the status line, second is the end of the response headers, in this case there is none.

```python
connection, client = server_socket.accept()  # wait for client

with connection:
	input = connection.recv(1024)
	connection.sendall(b"HTTP/1.1 200 OK\r\n\r\n")
server_socket.close()
```


## Respond with 404

Now we need to actually look at the content of the user request:

```
GET /index.html HTTP/1.1
Host: localhost:4221
User-Agent: curl/7.64.1
```

If the path is `/`.

```python
def parse_input(input: bytes) -> List[str]:
    input = input.decode()
    return input.split("\r\n")


def handle_request(input: bytes) -> bytes:
    # we expect requests like this:
    # GET /index.html HTTP/1.1
    # Host: localhost:4221
    # User-Agent: curl/7.64.1
    input = parse_input(input)
    method, path, version = input[0].split(" ")
    output = ""
    if path == "/":
        output = b"HTTP/1.1 200 OK\r\n\r\n"
    else:
        output = b"HTTP/1.1 404 Not Found\r\n\r\n"
    return output
```

## Respond with content

Now, a user request may look like:

```
GET /echo/abc HTTP/1.1
Host: localhost:4221
User-Agent: curl/7.64.1
```

And our response should be:

```
HTTP/1.1 200 OK
Content-Type: text/plain
Content-Length: 3

abc
```

I refactored my code for a little, and added code to handle `/echo`.

```python
def convert_to_output(input: List[str]) -> bytes:
    output = "\r\n".join(input) + "\r\n"
    return output.encode()


def handle_request(input: bytes) -> bytes:
    # we expect requests like this:
    # GET /index.html HTTP/1.1
    # Host: localhost:4221
    # User-Agent: curl/7.64.1
    input = parse_input(input)
    method, path, version = input[0].split(" ")
    output = []
    if path == "/":
        output.append("HTTP/1.1 200 OK")
        output.append("")  # empty body
    elif len(path) >= 5 and path[:5] == "/echo":
        body = path[6:]
        output.append("HTTP/1.1 200 OK")
        output.append("Content-Type: text/plain")
        output.append(f"Content-Length: {len(body)}")
        output.append("")
        output.append(body)
    else:
        output.append("HTTP/1.1 404 Not Found")
        output.append("")
    print(output)

    return convert_to_output(output)
```

## Parse headers

Now, if `/user-agent`, our response should be the `User-Agent`'s value.

```
GET /user-agent HTTP/1.1
Host: localhost:4221
User-Agent: curl/7.64.1
```

```
HTTP/1.1 200 OK
Content-Type: text/plain
Content-Length: 11

curl/7.64.1
```

Simply add code to handle this request:

```python
elif len(path) >= 11 and path[:11] == "/user-agent":
	body = input[2].split(": ")[1]
	output.append("HTTP/1.1 200 OK")
	output.append("Content-Type: text/plain")
	output.append(f"Content-Length: {len(body)}")
	output.append("")
	output.append(body)
```

## Concurrent connections

To handle concurrent connections, I would like to use `threading`.

```python
def handle_client(connection):
    with connection:
        while True:
            input = connection.recv(1024)
            connection.sendall(handle_request(input))


def main():
    # You can use print statements as follows for debugging, they'll be visible when running tests.
    print("Logs from your program will appear here!")

    server_socket = socket.create_server(("localhost", 4221), reuse_port=True)

    while True:
        connection, client = server_socket.accept()  # wait for client
        thread = threading.Thread(target=handle_client, args=(connection,))
        thread.start()
```

## Get a file

Our server should also accept an argument,

```
./your_server.sh --directory <directory>
```

Now, requests like `GET /files/<filename>` requires us to return the content of a file if the file is on the server within `<directory>`, the response should have a content type of `application/octet-stream`. Else return 404.

I used `argparse`,

```python
parser = argparse.ArgumentParser(description="start the server")
parser.add_argument("--directory", type=str)
args = parser.parse_args()
```

```python
elif len(path) >= 6 and path[:6] == "/files":
    file_path = os.path.join(args.directory, path[7:])
    print("file path: ", file_path)
    if os.path.isfile(file_path):
        body = ""
        with open(file_path, "r") as file:
            body = file.read()
        output.append("HTTP/1.1 200 OK")
        output.append("Content-Type: application/octet-stream")
        output.append(f"Content-Length: {len(body)}")
        output.append("")
        output.append(body)
    else:
        print("file not found")
        output.append("HTTP/1.1 404 Not Found")
        output.append("Content-Length: 0")
        output.append("")
```

## Post a file

For `POST /files/<filename>`, we need to store the request body into the specified file. Response code should be 201.

```python
elif method == "POST" and len(path) >= 6 and path[:6] == "/files":
    file_path = os.path.join(args.directory, path[7:])
    body = input[6]
    with open(file_path, "w") as file:
        file.write(body)
    output.append("HTTP/1.1 201 OK")
    output.append("")
```

## Conclusion

Now that the server is capable of handling various `GET` requests and `POST` requests, we can say that it is actually a complete server. In this project, I got to practice parsing different parts of HTTP requests, and return specific responses. If you'd like to view my complete code, please check out my [repo](https://github.com/sonnyding1/sonnyding1-codecrafters-http-server-python), thanks!