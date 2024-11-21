const fs = require('fs');
const crypto = require('crypto');

// Function to log events
function logEvent(nodeId, event) {
    const timestamp = new Date().toISOString();
    const hash = crypto.createHash('sha256').update(`${nodeId}:${timestamp}:${event}`).digest('hex');

    const logEntry = {
        nodeId,
        timestamp,
        event,
        hash
    };

    // Append to local log file
    fs.appendFileSync('events.log', JSON.stringify(logEntry) + '\n');

    console.log('Event logged:', logEntry);
}

// Example usage
logEvent('node-1', 'Transaction completed');

//python

import hashlib
import json
import requests
from datetime import datetime

# Function to log events
def log_event(node_id, event):
    timestamp = datetime.utcnow().isoformat()
    log_entry = {
        "node_id": node_id,
        "timestamp": timestamp,
        "event": event,
    }
    log_entry["hash"] = hashlib.sha256(json.dumps(log_entry).encode()).hexdigest()

    # Append to local log file
    with open("events.log", "a") as file:
        file.write(json.dumps(log_entry) + "\n")

    return log_entry

# Function to synchronize logs with another node
def sync_logs(node_url, log_entry):
    try:
        response = requests.post(f"{node_url}/sync", json=log_entry)
        if response.status_code == 200:
            print("Log synchronized successfully!")
        else:
            print("Failed to synchronize log:", response.text)
    except Exception as e:
        print("Error syncing logs:", str(e))

# Example usage
new_event = log_event("node-1", "New transaction")
sync_logs("http://other-node.com", new_event)
