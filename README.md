# 5a_Create_Socket_for_HTTP_for_webpage_upload_and_download
## AIM :
To write a PYTHON program for socket for HTTP for web page upload and download
## Algorithm

1.Start the program.
<BR>
2.Get the frame size from the user
<BR>
3.To create the frame based on the user request.
<BR>
4.To send frames to server from the client side.
<BR>
5.If your frames reach the server it will send ACK signal to client otherwise it will send NACK signal to client.
<BR>
6.Stop the program
<BR>
## Program 
AT SERVER

~~~~
import socket
import os

HOST = '127.0.0.1'
PORT = 8080

os.makedirs("uploads", exist_ok=True)

def handle_request(request):
    lines = request.split("\r\n")
    first_line = lines[0]
    method, path, _ = first_line.split()

    if method == "GET":
        body = "<html><body><h1>Hello, World!</h1></body></html>"
        response = (
            "HTTP/1.1 200 OK\r\n"
            f"Content-Length: {len(body)}\r\n"
            "Content-Type: text/html\r\n"
            "Connection: close\r\n\r\n"
            + body
        )
        return response.encode()

    elif method == "POST":
        request_bytes = request.encode()
        body_start = request.find("\r\n\r\n") + 4
        body = request[body_start:]
        filename = "uploads/upload.txt"
        with open(filename, "w") as f:
            f.write(body)
        response = (
            "HTTP/1.1 200 OK\r\n"
            "Content-Type: text/plain\r\n"
            "Connection: close\r\n\r\n"
            "File uploaded successfully!"
        )
        return response.encode()

    else:
        return b"HTTP/1.1 405 Method Not Allowed\r\n\r\n"

def run_server():
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.bind((HOST, PORT))
        s.listen(1)
        print(f"Server running at http://{HOST}:{PORT}")
        while True:
            conn, addr = s.accept()
            with conn:
                data = conn.recv(4096).decode()
                if not data:
                    continue
                response = handle_request(data)
                conn.sendall(response)

if __name__ == "__main__":
    run_server()
~~~~
AT CLIENT

~~~~
import socket

HOST = '127.0.0.1'
PORT = 8080

def http_get(path="/"):
    request = f"GET {path} HTTP/1.1\r\nHost: {HOST}\r\nConnection: close\r\n\r\n"
    with socket.create_connection((HOST, PORT)) as s:
        s.sendall(request.encode())
        response = s.recv(4096).decode()
    print("GET Response:\n", response)

def http_post(path="/upload", data="Hello upload!"):
    request = (
        f"POST {path} HTTP/1.1\r\n"
        f"Host: {HOST}\r\n"
        f"Content-Length: {len(data)}\r\n"
        "Connection: close\r\n\r\n"
        + data
    )
    with socket.create_connection((HOST, PORT)) as s:
        s.sendall(request.encode())
        response = s.recv(4096).decode()
    print("POST Response:\n", response)

if __name__ == "__main__":
    http_get("/")         
    http_post("/upload")  

~~~~
## OUTPUT 
<img width="500" height="272" alt="image" src="https://github.com/user-attachments/assets/f73bc24b-d2f0-4a0a-80fa-3d177eb2e042" />

## Result
Thus the socket for HTTP for web page upload and download created and Executed
