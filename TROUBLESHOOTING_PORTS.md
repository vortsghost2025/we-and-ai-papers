# Troubleshooting Port Issues

## Current Port Usage

Based on the system scan:
- Port 3000: Currently in use by a Node.js process (PID 25972)
- Port 4001: Available for use
- Other ports used by the system: 3001-3003, 4000

## Solution Options

### Option 1: Kill the existing process on port 3000 (if no longer needed)
```bash
taskkill /PID 25972 /F
```

### Option 2: Use a different port for the web interface

The `start-web-interface.js` script is configured to run on port 4001, which should be available. However, if you're still experiencing issues, the problem might be that multiple servers are trying to run simultaneously.

## How to properly start the web interface

1. First, stop any existing processes:
   ```bash
   # Find and note the PIDs of any running node processes
   tasklist | findstr node
   
   # If needed, kill them (be careful with this command)
   taskkill /PID <your_node_pid> /F
   ```

2. Then start the web interface:
   ```bash
   node start-web-interface.js
   ```

3. Access the dashboard at: `http://localhost:4001`

## Alternative: Check what's running on port 3000

The process on port 3000 might be a dashboard server that was started separately. You can check what it is by visiting `http://localhost:3000` in your browser.

## If nothing works

If you're still having issues, try this complete reset:

1. Kill all node processes:
   ```bash
   taskkill /IM node.exe /F
   ```

2. Wait a few seconds for ports to be released

3. Start the web interface:
   ```bash
   node start-web-interface.js
   ```

4. Access the dashboard at: `http://localhost:4001`