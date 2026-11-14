<p align="center">
  <img src="./logo.png" alt="IoT project logo" width="400">
</p>

# 🌐 IoT-based Earthquake Early Warning and Vital Utilities Shut-off System

This project implements an **IoT-based earthquake early warning system** that not only detects earthquakes but also **automatically shuts off critical utilities** such as electricity, gas, and water. The system is fully configurable and monitorable via the **Telegram bot** `@EEWiotbot`.

---

## 🎬 Demo

See the end-to-end workflow, from MQTT sensor readings and earthquake detection to alerts, utility shut-off, and Telegram monitoring.

[▶ Watch the demo on Google Drive](https://drive.google.com/file/d/1vybizoU7w1bVRLN1NNh0iGl46-Q6AEOh/view?usp=drive_link) · [Open the MP4 in this repository](./demo.mp4)

## Architecture Overview

<img width="823" height="640" alt="Untitled Diagram_1" src="https://github.com/user-attachments/assets/1cc80836-793c-463c-a7e9-f314c0a48b15" />


The system consists of the following components:

### 1. Sensors
- Accelerometer and velocity sensors.
- Sensors publish data in **SenML format** to MQTT topics.

### 2. Message Broker (MQTT)
- A broker such as **HiveMQ** is used to handle communication between sensors, controller, and actuators.

### 3. Controller
- Subscribes to sensor topics.  
- Retrieves thresholds and device information from the **Data Catalog** via REST API.  
- Sends commands to alarms and actuators.

### 4. Actuators
- **Buzzer alarms** and **flashing lights**  
- **Cut-off switches** for electricity, gas, and water

### 5. Telegram Bot
- View real-time sensor data  
- Manage and register sensors and actuators  
- Check system status and reconnect after a shutdown  
- Send commands for shutdown or recovery

### 6. Static Web Service
- Stores and manages sensor data  
- Provides **REST gateways** for the Telegram bot

### 7. Utils Module
The `utils/` folder contains reusable Python modules that are imported across services and other components:

- `config_loader.py` – for configuration management  
- `device_manager.py` – for device control and management  
- `device_registrar.py` – for registering devices in the system  
- `sensor_storage.py` – for managing sensor data storage  
- `telBot_graph_table_generator.py` – for Telegram bot visualization  
- `topic_fetcher.py` – for fetching MQTT topics  

---

## Prerequisites

- Python 3.14 or newer
- Access to an MQTT broker and REST catalog

Create and activate a virtual environment, then install the dependencies:

```bash
python3.14 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Copy `.env.example` to `.env` and set your Telegram bot token in it:

```dotenv
TELEGRAM_BOT_TOKEN=your-telegram-bot-token
```

The `.env` file is ignored by Git. Non-secret runtime settings are in `config/system_config.json`.

---

## Running the System

Run the system from the repository root:

```bash
python main.py
```

**Execution sequence:**  
1. `Data Catalog`  
2. `Static Web Service`  
3. Sensors: `Accelerometer` → `Velocity Sensor`  
4. `Controller`  
5. Actuators: `Buzzer Alarm` → `Flashing Light` → `Electricity/Gas/Water Cutoff`  
6. `Telegram Bot`

After startup, configure and monitor the system using the Telegram bot `@EEWiotbot`.

---

## Running Individual Modules

Each service, sensor, or actuator can also be run independently using:

```bash
python -m <folder>.<module_name>
```

**Examples:**

```bash
python -m services.Data_catalog
python -m sensors.accelerometer_sensor
python -m actuators.electricity_cutoff
python -m services.telegram_bot
```

---

## Testing

There is a `test/` folder for checking different parts of the system. All test modules can also be run independently using:
```bash
python -m test.<module_name>
```
After running main.py, you can use test scripts to add devices, and then you can use a test script to remove some of them.
To get sensor outputs, there is a subscribe test file that shows the incoming sensor values.
For threshold checking (accelerometer and velocity), two test files are provided where you can feed custom values and verify the behavior. Earthquake alerts and system performance can also be viewed in the terminal.
Note: the same flow can also be done through the Telegram bot.

**Available test modules:**

- `TEST_add_devices.py` – test device registration  
- `TEST_remove_sensors.py` – test device removal
- `TEST_mqtt_sub.py` – test MQTT subscription  
- `TEST_fake_accelerometer.py` – simulate accelerometer sensor data  
- `TEST_fake_velocitymeter.py` – simulate velocity sensor data  

---

## Configuration

The non-secret settings in `config/system_config.json` include REST endpoints, the MQTT broker, warning and cut-off thresholds, the sensor interval, and the earthquake detection time window. Set the Telegram bot token separately in `.env` as described above.

## Directory Structure

```
.
│
├── config/                 # Runtime configuration
├── data/                   # Sensor data
├── sensors/                # Accelerometer and velocity sensors
├── actuators/              # Buzzer, flashing light, and cut-off actuators
├── services/               # Data catalog, web service, controller, Telegram bot
├── utils/                  # Reusable utility modules
├── test/                   # Testing scripts
├── .env.example            # Environment variable template
├── Diagram_of_Use_Case.jpg # System diagram
├── LICENSE
├── README.md
├── demo.mp4
├── main.py
├── pyproject.toml
├── requirements.txt
└── uv.lock

```

## License

This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 International License. See [LICENSE](./LICENSE) for the terms. Commercial use requires the author's written permission.
