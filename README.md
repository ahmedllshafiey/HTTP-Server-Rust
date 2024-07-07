# Simple HTTP Server

This is a basic HTTP server written in Rust. It listens on `127.0.0.1:8080` and serves static HTML files. The server responds with a simple "Hello, world!" message when accessed via the root URL ("/") and returns a 404 error for any other requests.

## Getting Started

### Prerequisites

To run this server, you need to have Rust installed on your machine. If you don't have Rust installed, you can download it from [rust-lang.org](https://www.rust-lang.org/tools/install).

### Installation

1. Clone the repository (or copy the code into a new Rust project).
2. Ensure you have the necessary HTML files (`hello.html` and `404.html`) in the project directory.

### Running the Server

1. Open a terminal and navigate to the project directory.
2. Build the project using Cargo:
    ```sh
    cargo build
    ```
3. Run the server:
    ```sh
    cargo run
    ```
4. Open your web browser and navigate to `http://127.0.0.1:8080`. You should see the contents of `hello.html`.

## Code Overview

### Main Function

The `main` function sets up the TCP listener on `127.0.0.1:8080` and waits for incoming connections. For each incoming connection, it calls the `handle_connection` function.

```rust
fn main() {
    let listener = TcpListener::bind("127.0.0.1:8080").unwrap();
    for stream in listener.incoming() {
        let stream = stream.unwrap();
        handle_connection(stream);
    }
}
```

### Handling Connections

The `handle_connection` function reads data from the stream into a buffer and checks if the request is a GET request for the root URL ("/"). Depending on the request, it serves either `hello.html` or `404.html`.

```rust
fn handle_connection(mut stream: TcpStream) {
    let mut buffer = [0; 1024];
    stream.read(&mut buffer).unwrap();

    let get = b"GET / HTTP/1.1\r\n";

    let (status_line, filename) = if buffer.starts_with(get) {
        ("HTTP/1.1 200 OK\r\n\r\n", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND\r\n\r\n", "404.html")
    };
    let contents = fs::read_to_string(filename).unwrap();

    let response = format!("{status_line}{contents}");
    stream.write_all(response.as_bytes()).unwrap();
    stream.flush().unwrap();
}
```

### Files

- `hello.html`: The HTML file served for a successful GET request to the root URL.
- `404.html`: The HTML file served for any other request, indicating a 404 Not Found error.

## Customization

- To change the port, modify the line:
  ```rust
  let listener = TcpListener::bind("127.0.0.1:8080").unwrap();
  ```
  Replace `8080` with your desired port number.
  
- To change the response content, edit `hello.html` and `404.html` files.

## Error Handling

This example uses basic error handling with `unwrap()`. For a more robust implementation, consider handling errors gracefully using pattern matching or the `?` operator.

## License

This project is licensed under the MIT License.

---