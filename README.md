# Set maximum power limits
MAX_POWER = 92  # Maximum total power available
MAX_DEVICE_POWER = 40  # Maximum power a single device can consume

# Variables to keep track of the system state
current_power_usage = 0  # Tracks the total power being used
devices = []  # List of devices currently using power
device_power = {}  # Dictionary to track how much power each device is using

# Function to update the total power usage
def update_power_usage():
    total_power = 0
    for device in devices:
        total_power += device_power[device]
    return total_power

# Function to handle a new device connection
def connect_device(device_id):
    if current_power_usage < MAX_POWER:
        available_power = MAX_POWER - current_power_usage
        power_for_device = min(MAX_DEVICE_POWER, available_power)
        device_power[device_id] = power_for_device
        devices.append(device_id)
        current_power_usage += power_for_device
        print(f"Device {device_id} connected with {power_for_device} units.")
    else:
        print(f"Device {device_id} cannot connect, not enough power.")

# Function to handle device disconnection
def disconnect_device(device_id):
    if device_id in device_power:
        released_power = device_power[device_id]
        current_power_usage -= released_power
        devices.remove(device_id)
        del device_power[device_id]
        print(f"Device {device_id} disconnected.")
    else:
        print(f"Device {device_id} is not connected.")

# Function to handle device power consumption change
def change_device_power(device_id, new_power):
    if device_id in device_power:
        current_power_usage -= device_power[device_id]
        device_power[device_id] = min(new_power, MAX_POWER - current_power_usage)
        current_power_usage += device_power[device_id]
        print(f"Device {device_id} updated power to {device_power[device_id]} units.")
    else:
        print(f"Device {device_id} is not connected.")

# Main function to process the events
def process_events(events):
    for event in events:
        device_id = event["device_id"]
        action = event["action"]
        
        if action == "connect":
            connect_device(device_id)
        elif action == "disconnect":
            disconnect_device(device_id)
        elif action == "change":
            change_device_power(device_id, event["new_power"])

# Example events
events = [
    {"device_id": "A", "action": "connect"},
    {"device_id": "B", "action": "connect"},
    {"device_id": "C", "action": "connect"},
    {"device_id": "A", "action": "change", "new_power": 20},
    {"device_id": "B", "action": "disconnect"}
]

# Process the events
process_events(events)
