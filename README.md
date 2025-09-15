# HTTP Web Server

A multi-threaded HTTP/1.1 compliant web server implementation in C++ using Windows Sockets API (Winsock). This server supports multiple simultaneous connections, multi-language content delivery, and full CRUD operations.

## Author
**Ori Braverman**

## Table of Contents
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Supported HTTP Methods](#supported-http-methods)
- [Multi-Language Support](#multi-language-support)
- [API Examples](#api-examples)
- [File Structure](#file-structure)
- [Technical Details](#technical-details)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

## Features

- **HTTP/1.1 Compliance**: Full support for HTTP/1.1 protocol
- **Multi-threaded**: Handles up to 60 simultaneous connections
- **Multi-language Content**: Serves content in English, French, and Hebrew
- **Complete CRUD Operations**: GET, POST, PUT, DELETE support
- **Static File Serving**: Serves HTML files and other static content
- **Connection Management**: Automatic timeout and cleanup of inactive connections
- **Error Handling**: Proper HTTP status codes and error responses
- **Query String Support**: Language selection via URL parameters

## Architecture

The server follows a modular architecture with clear separation of concerns:

```
┌─────────────────────────────────────┐
│           Client Requests           │
└─────────────┬───────────────────────┘
              │
┌─────────────▼───────────────────────┐
│         Main Server Loop            │
│       (server3.cpp)                 │
│  - Socket Management                │
│  - Connection Handling              │
│  - Request/Response Coordination    │
└─────────────┬───────────────────────┘
              │
┌─────────────▼───────────────────────┐
│      Server Utilities               │
│    (ServerUtilities.cpp/.h)        │
│  - HTTP Method Processing           │
│  - File Operations                  │
│  - Response Generation              │
│  - Language Detection               │
└─────────────┬───────────────────────┘
              │
┌─────────────▼───────────────────────┐
│        Static Content               │
│      (HTML_FILES/)                  │
│  - Multi-language HTML files       │
│  - Error pages                     │
│  - Test content                    │
└─────────────────────────────────────┘
```

## Prerequisites

- **Operating System**: Windows (uses Windows Sockets API)
- **Compiler**: Microsoft Visual Studio 2019 or later (v142 toolset)
- **SDK**: Windows 10 SDK
- **Network**: Available port 27015

## Installation

### 1. Clone the Repository
```bash
git clone https://github.com/OriBraverman/HTTP-Web-Server.git
cd HTTP-Web-Server
```

### 2. Extract HTML Content
Extract the `HTML_FILES.rar` archive to `C:\Temp\`:
```
C:\Temp\HTML_FILES\
├── English\
│   └── index.html
├── French\
│   └── index.html
├── Hebrew\
│   └── index.html
├── Error\
│   └── error.html
├── fileToAdd.txt
└── fileToDelete.txt
```

### 3. Build the Project
1. Open `Server3\Server3.sln` in Visual Studio
2. Select your preferred configuration (Debug/Release)
3. Build the solution (Ctrl+Shift+B)

## Configuration

### Server Configuration
The server configuration is defined in `ServerUtilities.h`:
- **Port**: 27015 (HTTP_PORT constant)
- **Max Connections**: 60 (MAX_SOCKETS constant)
- **Buffer Size**: 2048 bytes (BUFFER_SIZE constant)
- **Connection Timeout**: 120 seconds

### Content Directory Structure
The server expects content to be organized as follows:
```
C:\Temp\HTML_FILES\
├── English\          # English content
├── French\           # French content  
├── Hebrew\           # Hebrew content
└── Error\            # Error pages
```

## Usage

### Starting the Server
1. Run the compiled executable from Visual Studio or command line
2. The server will start and display:
   ```
   Waiting for client to connect to the server.
   ```
3. The server is now ready to accept connections on `http://localhost:27015`

### Basic Usage Examples
- **Access English content**: `http://localhost:27015/index.html`
- **Access French content**: `http://localhost:27015/index.html?lang=fr`
- **Access Hebrew content**: `http://localhost:27015/index.html?lang=he`

## Supported HTTP Methods

### GET
Retrieves static content from the server.
```http
GET /index.html HTTP/1.1
Host: localhost:27015
```
- **Response**: Returns the requested file content
- **Status Codes**: 200 (OK), 404 (Not Found)

### POST
Sends data to the server for processing.
```http
POST /endpoint HTTP/1.1
Host: localhost:27015
Content-Length: 13

Hello, Server!
```
- **Response**: Echoes received data to console
- **Status Code**: 200 (OK)

### PUT
Creates or updates files on the server.
```http
PUT /newfile.txt HTTP/1.1
Host: localhost:27015
Content-Length: 20

This is new content!
```
- **Status Codes**: 
  - 200 (OK) - File updated
  - 201 (Created) - New file created
  - 204 (No Content) - Empty content
  - 412 (Precondition Failed) - Error

### DELETE
Removes files from the server.
```http
DELETE /fileToDelete.txt HTTP/1.1
Host: localhost:27015
```
- **Status Codes**:
  - 200 (OK) - File deleted successfully
  - 204 (No Content) - File not found or delete failed

### HEAD
Returns headers only (no body content).
```http
HEAD /index.html HTTP/1.1
Host: localhost:27015
```
- **Response**: Headers with content information
- **Status Codes**: 200 (OK), 404 (Not Found)

### OPTIONS
Returns supported HTTP methods.
```http
OPTIONS / HTTP/1.1
Host: localhost:27015
```
- **Response**: `Allow: OPTIONS, GET, HEAD, POST, PUT, DELETE, TRACE`
- **Status Code**: 204 (No Content)

### TRACE
Echoes the received request back to the client.
```http
TRACE / HTTP/1.1
Host: localhost:27015
```
- **Response**: Original request message
- **Status Code**: 200 (OK)

## Multi-Language Support

The server supports content in multiple languages through query string parameters:

### Supported Languages
| Language | Query Parameter | Example |
|----------|----------------|---------|
| English (default) | `lang=en` or no parameter | `index.html` |
| French | `lang=fr` | `index.html?lang=fr` |
| Hebrew | `lang=he` | `index.html?lang=he` |

### Language Code Mapping
The server supports various language code variants:
- **English**: `en`, `en-US`, `en-GB`, `en-AU`, `en-CA`, etc.
- **French**: `fr`, `fr-FR`, `fr-CA`, `fr-CH`, `fr-BE`, etc.
- **Hebrew**: `he`, `he-IL`

### Content Examples
- **English**: Britain's National Dishes
- **French**: French cuisine and culture
- **Hebrew**: Israeli content with RTL support

## API Examples

### Using cURL

#### GET Request
```bash
curl http://localhost:27015/index.html
```

#### POST Request
```bash
curl -X POST -d "Hello from client!" http://localhost:27015/
```

#### PUT Request
```bash
curl -X PUT -d "New file content" http://localhost:27015/newfile.txt
```

#### DELETE Request
```bash
curl -X DELETE http://localhost:27015/fileToDelete.txt
```

#### Language-specific Request
```bash
curl http://localhost:27015/index.html?lang=fr
```

### Using PowerShell
```powershell
# GET request
Invoke-WebRequest -Uri "http://localhost:27015/index.html"

# POST request
Invoke-WebRequest -Uri "http://localhost:27015/" -Method POST -Body "Hello Server"

# PUT request
Invoke-WebRequest -Uri "http://localhost:27015/test.txt" -Method PUT -Body "Test content"
```

## File Structure

```
HTTP-Web-Server/
├── README.md                 # This documentation
├── README.txt               # Original brief documentation
├── HTML_FILES.rar           # Static content archive
└── Server3/                 # Main server implementation
    ├── Server3.sln          # Visual Studio solution
    ├── Server3.vcxproj      # Project file
    ├── server3.cpp          # Main server logic
    ├── ServerUtilities.h    # Server utilities header
    └── ServerUtilities.cpp  # Server utilities implementation
```

### Core Files Description

#### `server3.cpp`
- Main entry point and server loop
- Socket initialization and management
- Connection acceptance and handling
- Select-based I/O multiplexing

#### `ServerUtilities.h`
- Data structures and enums
- Function declarations
- Configuration constants

#### `ServerUtilities.cpp`
- HTTP method implementations
- File operations (read, write, delete)
- Response generation
- Language detection logic
- Connection management utilities

## Technical Details

### Socket Management
- Uses Winsock2 API for Windows networking
- Non-blocking sockets with select() for multiplexing
- Maximum 60 concurrent connections
- Automatic cleanup of inactive connections (2-minute timeout)

### HTTP Implementation
- HTTP/1.1 compliant responses
- Proper status codes and headers
- Content-Type detection
- Content-Length calculation
- Date headers with current timestamp

### Memory Management
- Fixed-size buffers (2048 bytes)
- Proper buffer cleanup after each request
- Memory reuse for connection structures

### Error Handling
- Comprehensive error checking for all socket operations
- Graceful handling of client disconnections
- Proper resource cleanup on errors
- HTTP error responses for invalid requests

## Troubleshooting

### Common Issues

#### Port Already in Use
```
Error at bind(): 10048
```
**Solution**: Ensure port 27015 is not being used by another application.

#### HTML Files Not Found
```
404 Not Found
```
**Solution**: Verify HTML_FILES is extracted to `C:\Temp\HTML_FILES\`

#### Connection Timeout
**Solution**: Check if the client request is complete and properly formatted.

#### Build Errors
**Solution**: 
- Ensure Windows 10 SDK is installed
- Verify Visual Studio version compatibility
- Check that Ws2_32.lib is properly linked

### Debug Mode
The server provides verbose console output including:
- Connection establishment/termination
- Request details
- Response information
- Error messages

### Performance Considerations
- Maximum 60 simultaneous connections
- 2-minute connection timeout
- 2048-byte buffer limitation
- Synchronous file I/O operations

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Guidelines
- Follow existing code style and patterns
- Add appropriate error handling
- Update documentation for new features
- Test with multiple browsers and HTTP clients

## License

This project is part of an educational implementation. Please respect the original author's work and provide appropriate attribution when using or modifying this code.

---

For questions or support, please refer to the project repository or contact the author.