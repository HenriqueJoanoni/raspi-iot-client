# IoT Environmental Monitoring System

A complete IoT project using **Raspberry Pi 4**, **Python**, **Flask**, **PubNub**, **AWS EC2**, and various sensors.
This project collects environmental data (temperature, humidity, light intensity, and motion), sends it to a cloud
backend, stores it in a database, and displays real‑time + historical dashboards.

---

## 🚀 Project Overview

This project demonstrates an end‑to‑end IoT architecture suitable for academic and professional use:

* **Raspberry Pi** reads sensor data (DHT22, Photo Transistor via MCP3008, PIR).
* **Python scripts** process data and publish to **PubNub**.
* **Flask API** (running on AWS EC2) receives data and stores it in a **PostgreSQL** or **SQLite** database.
* A web dashboard visualizes real‑time and historical sensor readings.

This repository contains the backend and Raspberry Pi code, along with deployment instructions.

---

## 🧰 Hardware Used

* **Raspberry Pi 4 (8GB RAM)**
* **DHT22 Temperature + Humidity Sensor** (module version with built‑in pull‑up resistor)
* **Photo Transistor Light Sensor** (analog)
* **MCP3008 ADC** (to read analog signals from the photo transistor)
* **PIR Motion Sensor** (HC‑SR501)
* Breadboard + Jumper Wires
* USB‑C power supply

---

## 🧪 Sensor Summary

| Sensor               | Type    | Pi Compatibility         | Notes                               |
|----------------------|---------|--------------------------|-------------------------------------|
| **DHT22**            | Digital | Direct GPIO              | No resistor needed (module version) |
| **Photo Transistor** | Analog  | ❌ Pi has no analog input | ✔ Requires MCP3008                  |
| **PIR Sensor**       | Digital | Direct GPIO              | Easy 3‑wire connection              |

---

## 🧩 Raspberry Pi Wiring Overview

### MCP3008 → Raspberry Pi (SPI)

* VDD → 3.3V
* VREF → 3.3V
* AGND → GND
* DGND → GND
* CLK → GPIO11 (Pin 23)
* DOUT → GPIO9 (Pin 21)
* DIN → GPIO10 (Pin 19)
* CS → GPIO8 (Pin 24)

### Sensors

* DHT22 → 3.3V, GND, GPIO4
* Photo Transistor → MCP3008 CH0
* PIR → 5V, GND, GPIO17

---

## 🖥 Backend Overview (Flask API)

The backend handles:

* Receiving sensor data from PubNub
* Storing readings in a database
* Exposing REST endpoints
* Providing dashboard data

### Features

* `/api/sensor-data` → POST data from the Pi
* `/api/latest` → latest readings
* `/api/history?start=...&end=...` → historical data

---

## 🗄 Database

You may choose:

* **PostgreSQL** (recommended for production + AWS)
* **SQLite** (easiest for development/testing)

The backend uses **SQLAlchemy** for ORM and **Flask‑Migrate** for migrations.

Example model:

```python
class SensorReading(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    device_id = db.Column(db.String(50))
    temperature = db.Column(db.Float)
    humidity = db.Column(db.Float)
    light = db.Column(db.Float)
    motion = db.Column(db.Boolean)
    timestamp = db.Column(db.DateTime, default=datetime.utcnow)
```

---

## 🐍 Python Virtual Environment Setup

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Create `requirements.txt`:

```
Flask
Flask_SQLAlchemy
Flask_Migrate
psycopg2-binary
PubNub
gpiozero
adafruit-circuitpython-dht
spidev
RPi.GPIO
```

---

## 🌐 PubNub Integration

The project uses PubNub for:

* Real‑time streaming to the cloud
* Pi → EC2 communication
* Optional remote commands (e.g., trigger LED, send alerts)

Pi publishes sensor data → Flask consumes it.

---

## ☁ Deployment (AWS EC2)

Steps:

1. Launch Ubuntu EC2 instance
2. Install Python & dependencies
3. Clone this repo
4. Set environment variables for Flask
5. Run Flask with Gunicorn
6. Reverse proxy with Nginx
7. Enable HTTPS with Certbot

---

## 🗂 Project Structure

```
raspi-iot-client/
├── app/
│   ├── __init__.py          # app factory
│   ├── config.py
│   ├── extensions.py        # db, migrate, etc
│   ├── models/
│   │   ├── __init__.py
│   │   └── sensor.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── ingest.py        # POST /api/sensor-data
│   │   ├── query.py         # GET /api/readings, /api/latest
│   │   └── admin.py         # alerts, stats
│   ├── services/
│   │   ├── pubnub_service.py
│   │   └── alerting.py
│   └── utils.py
│
├── migrations/              # flask-migrate
├── tests/
│
├── .env.example
├── requirements.txt
├── manage.py                # CLI for running & migrations
├── gunicorn.conf.py
└── README.md
```

---
## 🙌 Credits

Created for IoT module using Raspberry Pi, Python, Flask, PubNub, and AWS.
