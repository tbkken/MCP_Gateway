# MCP Manager

MCP Manager is a web-based application for managing Model Context Protocol (MCP) services. It provides a user-friendly interface to configure, test, and monitor MCP services, as well as view the tools available from those services.

## Features

- **Service Management**: View, add, and delete MCP services
- **Connection Testing**: Test the connectivity of MCP services
- **Tool Discovery**: View available tools from configured MCP services
- **Web Interface**: Intuitive UI built with Vue.js and Element Plus
- **Persistent Storage**: SQLite database for storing service configurations

## Technology Stack

- **Backend**: Spring Boot 2.7.18 (Java 11)
- **Frontend**: Vue 3.3.4, Element Plus 2.3.4
- **Database**: SQLite
- **Build Tool**: Maven

## Project Structure

```
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/mcpmanager/
│   │   │       ├── McpManagerApplication.java
│   │   │       ├── controller/
│   │   │       ├── entity/
│   │   │       ├── repository/
│   │   │       └── service/
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── static/
│   │       │   └── index.html
│   │       └── templates/
├── pom.xml
└── mcp.db
```

## Getting Started

### Prerequisites

- Java 11 or higher
- Maven 3.6 or higher

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/tbkken/MCP_Gateway.git
   cd MCP_Gateway
   ```

2. Build the project:
   ```bash
   mvn clean package
   ```

3. Run the application:
   ```bash
   java -jar target/mcp-manager-0.0.1-SNAPSHOT.jar
   ```

### Running with Maven

Alternatively, you can run the application directly with Maven:
```bash
mvn spring-boot:run
```

## Usage

After starting the application, open your browser and navigate to `http://localhost:8080`.

### Managing MCP Services

1. **Add a Service**:
   - Click the "新增MCP服务" (Add MCP Service) button
   - Fill in the service details:
     - Name: A unique identifier for the service
     - Type: Service type (SSE or HTTP)
     - URL: The endpoint URL for the MCP service
   - Optional settings:
     - Timeout: Connection timeout in seconds
     - Headers: Custom HTTP headers for authentication
   - Click "测试" (Test) to verify the connection
   - Click "确定" (Confirm) to save the service

2. **View Tools**:
   - Click "查看工具" (View Tools) on any service card
   - See the list of available tools with their input and output schemas

3. **Delete a Service**:
   - Click "删除" (Delete) on any service card
   - Confirm the deletion in the dialog

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/mcp-servers` | Get all MCP services |
| GET | `/api/mcp-servers/{id}` | Get a specific MCP service by ID |
| POST | `/api/mcp-servers` | Create or update an MCP service |
| DELETE | `/api/mcp-servers/{id}` | Delete an MCP service by ID |
| POST | `/api/mcp-servers/test` | Test connection to an MCP service |
| GET | `/api/mcp-servers/{id}/tools` | Get tools available from an MCP service |

### Example Service Configuration

```json
{
  "name": "user info system",
  "type": "sse",
  "url": "http://127.0.0.1:10002/sse/",
  "timeout": 60,
  "headers": {
    "Authorization": "Bearer 54bfe0d6-803f-4f60-9bed-0d7709731011"
  }
}
```

## Database

The application uses SQLite for data persistence. The database file (`mcp.db`) is automatically created when the application starts.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a pull request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details if it exists.

## Acknowledgments

- Built with [Spring Boot](https://spring.io/projects/spring-boot)
- UI powered by [Vue.js](https://vuejs.org/) and [Element Plus](https://element-plus.org/)
- Database management with [SQLite](https://www.sqlite.org/)
