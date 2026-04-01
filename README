# FleetBook — CST8917 Lab 3
**Vehicle Booking System with Azure Service Bus, Logic Apps & Azure Functions**

---

## Demo Video
Link: https://youtu.be/WYy3J-wKuJk

---

## Architecture Overview

A customer submits a booking through the FleetBook web app → message flows into a **Service Bus Queue** → a **Logic App** picks it up and calls an **Azure Function** that checks fleet availability and calculates pricing → the Logic App branches based on the result, sending a confirmation or rejection email → the result is published to a **Service Bus Topic** with filtered subscriptions routing confirmed and rejected bookings to separate consumers.

```
Web App → SB Queue → Logic App → Azure Function → SB Topic → confirmed-sub / rejected-sub
```

---

## Services Used

| Service | Resource Name | Role |
|---------|--------------|------|
| Service Bus Namespace | `ngab-booking-sb1` | Messaging infrastructure |
| Service Bus Queue | `booking-queue` | Receives incoming booking requests |
| Service Bus Topic | `booking-results` | Publishes booking decisions |
| Topic Subscription | `confirmed-sub` | Filters confirmed bookings |
| Topic Subscription | `rejected-sub` | Filters rejected bookings |
| Logic App | `process-booking` | Orchestrates the full workflow |
| Azure Function | `ngab0016-fleetbook-func` | Evaluates fleet availability & pricing |
| Web Client | `client.html` | FleetBook booking web app |

---

## Project Structure

```
FleetBookFunctionApp/
├── function_app.py              # Azure Function — fleet evaluation & pricing logic
├── requirements.txt             # Python dependencies
├── test-function.http           # REST Client test requests
├── client.html                  # FleetBook web app
├── local.settings.example.json  # Settings template (no real keys)
└── README.md                    # This file
```

---

## Setup Instructions

### Prerequisites
- Python 3.11 or 3.12
- Azure Functions Core Tools
- VS Code with Azure Functions extension
- Azurite (local storage emulator)
- An Azure subscription

---

### 1. Clone the Repository

```bash
git clone https://github.com/ngab0016/cst8917-lab3-fleetbook.git
cd cst8917-lab3-fleetbook
```

---

### 2. Set Up the Azure Function Locally

Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate  # Mac/Linux
.venv\Scripts\activate     # Windows
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Copy the example settings file and fill in your values:

```bash
cp local.settings.example.json local.settings.json
```

Start Azurite and run the function locally:

```bash
# Press F1 → Azurite: Start in VS Code
# Press F5 to start the Function App
```

---

### 3. Azure Infrastructure Setup

#### Service Bus
1. Create a Service Bus namespace (Standard tier)
2. Create a queue named `booking-queue`
3. Create a topic named `booking-results`
4. Create subscription `confirmed-sub` with SQL filter: `sys.label = 'confirmed'`
5. Create subscription `rejected-sub` with SQL filter: `sys.label = 'rejected'`

#### Azure Function
Deploy via VS Code:
- F1 → `Azure Functions: Deploy to Function App`

Verify deployment:
```
https://<your-function-app>.azurewebsites.net/api/health
```

#### Logic App
Create a Consumption (Multi-tenant) Logic App named `process-booking` with this workflow:
1. **Trigger:** When a message is received in `booking-queue`
2. **Decode Message:** `base64ToString(triggerBody()?['ContentData'])`
3. **Parse Booking Request:** Parse the decoded JSON
4. **Call Check-Booking Function:** HTTP POST to the Azure Function
5. **Parse Function Response:** Parse the function's JSON response
6. **Condition:** If `status` equals `confirmed`
   - **True:** Send confirmation email → Publish to topic with label `confirmed`
   - **False:** Send rejection email → Publish to topic with label `rejected`

---

### 4. Run the Web Client

1. Open `client.html` in your browser
2. Click **Service Bus Configuration**
3. Enter your namespace name and SAS Primary Key
4. Submit a booking request

---

## Test Cases

| Test | Vehicle Type | Location | Expected Result |
|------|-------------|----------|-----------------|
| Confirmed | Sedan | Ottawa | ✅ Confirmed — V001 |
| Rejected | Sedan | Montreal | ❌ Rejected — unavailable |
| Confirmed | SUV | Toronto | ✅ Confirmed — V002 |
| Rejected | Truck | Toronto | ❌ Rejected — no trucks |

---

## Security Notes

- `local.settings.json` is excluded from version control via `.gitignore`
- SAS keys are never committed to the repository
- In production, use Managed Identity instead of connection strings

---

*CST8917 — Serverless Applications | Winter 2026 | Algonquin College*
