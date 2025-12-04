# Frontend Service

The Frontend Service provides a Streamlit-based web interface for the Data Collector ADIA system. It allows users to create browser automation tasks, view task history, and monitor task execution via gRPC connections to the Backend and Database services.

## Overview

This service provides:
- Web-based UI for task management
- Real-time task status updates
- Task history viewing
- Task creation and monitoring

## Features

- Modern Streamlit UI
- Real-time status updates via polling
- Task history retrieval and display
- Task status visualization
- Step-by-step execution viewing
- gRPC-based communication for better performance

## Installation

### Prerequisites

- Python 3.11 or higher
- Backend Service running (port 50050)
- Database Service running (port 50052)

### Setup

1. Install Python dependencies:
```bash
pip install -r requirements.txt
```

2. Generate protobuf files (first time only):
```bash
cd ../shared
python generate_protos.py
cd ../Front-end
```

## Configuration

### Environment Variables

```bash
BACKEND_SERVICE_HOST=localhost          # Backend Service host (default: localhost)
BACKEND_SERVICE_PORT=50050              # Backend Service port (default: 50050)
DATABASE_SERVICE_HOST=localhost         # Database Service host (default: localhost)
DATABASE_SERVICE_PORT=50052             # Database Service port (default: 50052)
```

### Streamlit Configuration

Create `.streamlit/config.toml` for custom settings:

```toml
[server]
port = 8501
address = "0.0.0.0"

[browser]
gatherUsageStats = false
```

### Default Ports

- **Streamlit UI**: `8501`
- **Backend Service**: `50050` (remote)
- **Database Service**: `50052` (remote)

## Usage

### Start the Service

**For Testing (Single Machine):**
```bash
# Run in a screen session
screen -S frontend-service
streamlit run app.py
# Press Ctrl+A then D to detach
```

**For Production (Separate Machine):**
```bash
export BACKEND_SERVICE_HOST=backend-service-host
export BACKEND_SERVICE_PORT=50050
export DATABASE_SERVICE_HOST=database-service-host
export DATABASE_SERVICE_PORT=50052
streamlit run app.py --server.port=8501 --server.address=0.0.0.0
```

### Access the UI

Open your browser and navigate to:
```
http://localhost:8501
```

## Features

### Create Task

1. Navigate to "Create Task" page
2. Enter task prompt (e.g., "Search for browser automation on DuckDuckGo")
3. Set max steps (default: 100)
4. Select browser type
5. Click "Start Task"

### View Tasks

1. Navigate to "Task List" page
2. See all tasks with status indicators:
   - 🟠 Running
   - 🟢 Completed
   - 🔴 Failed
   - 🔵 Pending
   - ⚫ Cancelled
3. Click "View History" to see execution details

### View Task History

1. Navigate to "Task History" page
2. Enter task ID or select from list
3. View step-by-step execution details in expandable sections

## Architecture

The frontend uses:
- **Streamlit** for the web UI framework
- **gRPC** for communication with Backend and Database services
- **Protocol Buffers** for type-safe message definitions

### Service Communication

- **Backend Service**: Task creation, status checking, cancellation
- **Database Service**: Task listing, history retrieval

## gRPC Integration

The frontend connects to services via gRPC:

### Backend Service Methods
- `StartTask` - Create and start a new task
- `GetTaskStatus` - Get current task status
- `CancelTask` - Cancel a running task

### Database Service Methods
- `ListTasks` - List all tasks
- `GetTask` - Get task details
- `GetTaskHistory` - Get task execution history

## Troubleshooting

### Cannot Connect to Backend Service

```bash
# Verify Backend Service is running
grpcurl -plaintext localhost:50050 list

# Check environment variables
echo $BACKEND_SERVICE_HOST
echo $BACKEND_SERVICE_PORT
```

### Cannot Connect to Database Service

```bash
# Verify Database Service is running
grpcurl -plaintext localhost:50052 list

# Check environment variables
echo $DATABASE_SERVICE_HOST
echo $DATABASE_SERVICE_PORT
```

### Protobuf Import Errors

```bash
# Regenerate protobuf files
cd ../shared
python generate_protos.py
```

### Port Already in Use

```bash
# Check what's using the port
lsof -i :8501  # Linux/Mac
netstat -an | findstr 8501  # Windows

# Change port
streamlit run app.py --server.port=8502
```

### Streamlit Not Found

```bash
# Install Streamlit
pip install streamlit
```

## Development

### Project Structure
```
Front-end/
├── app.py               # Streamlit application
├── requirements.txt     # Python dependencies
├── QUICKSTART.md        # Quick start guide
└── README.md            # This file
```

### Running in Development

```bash
# Start Streamlit in development mode
streamlit run app.py
```

### Adding Features

To add new features:
1. Update `app.py` with new Streamlit components
2. Add new gRPC method calls as needed
3. Update UI layout and styling

## Deployment

### Single Machine (Testing)

Run in a screen session:
```bash
screen -S frontend-service
streamlit run app.py
```

### Separate Machine (Production)

1. Install dependencies
2. Configure service addresses
3. Set environment variables
4. Run: `streamlit run app.py`
5. Expose port 8501

### Production Considerations

- Use reverse proxy (Nginx) for HTTPS
- Configure CORS if needed
- Set up monitoring
- Use process manager (systemd, supervisor)

## Quick Start

See [QUICKSTART.md](QUICKSTART.md) for a quick setup guide.

## License

Part of the Data Collector ADIA project.
