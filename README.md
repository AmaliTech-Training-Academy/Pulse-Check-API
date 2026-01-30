# Pulse-Check-API ("Watchdog" Sentinel)
This challenge is designed to test your ability to bridge Computer Science fundamentals with Modern Backend Engineering.

## 1. Business Context
> **Client:** *CritMon Servers Inc.* (A Critical Infrastructure Monitoring Company).

### The Problem
CritMon provides monitoring for remote solar farms and unmanned weather stations in areas with poor connectivity. These devices are supposed to send "I'm alive" signals every hour.

Currently, CritMon has no way of knowing if a device has gone offline (due to power failure or theft) until a human manually checks the logs. They need a system that alerts *them* when a device *stops* talking.

### The Solution
You need to build a **Dead Man’s Switch API**. Devices will register a "monitor" with a countdown timer (e.g., 60 seconds). If the device fails to "ping" (send a heartbeat) to the API before the timer runs out, the system automatically triggers an alert.

---

## 2. Technical Objective
Build a backend service that manages stateful timers.

* **Registration:** Allow a client to create a monitor with a specific timeout duration.
* **Heartbeat:** Reset the countdown when a ping is received.
* **Trigger:** Fire a webhook (or log a critical error) if the countdown reaches zero.


---

## 3. Getting Started

1.  **Fork this Repository:** Do not clone it directly. Create a fork to your own GitHub account.
2.  **Environment:** You may use **Node.js, Python, or Go**.
3.  **Submission:** Your final submission will be a link to your forked repository containing:
    * The source code.
    * The **Architecture Diagram** (See Section 4).
    * The `README.md` with documentation.

---

## 4. The Architecture Diagram (Design First)
**Task:** Before you write any code, you must design the logic flow.
**Deliverable:** A **Sequence Diagram** or **State Flowchart** embedded in your `README.md`.

**What the diagram must show:**
1.  **The Lifecycle:** The flow of data from `Monitor Creation` -> `Timer Start` -> `Heartbeat Received` -> `Timer Reset`.
2.  **The Failure State:** The flow when no heartbeat is received: `Timer Expiry` -> `Trigger Alert`.
3.  **Concurrency:** How the system handles multiple monitors running simultaneously.

*Tip: You can use Mermaid.js, Lucidchart, Draw.io, or a clear photo of a hand-drawn whiteboard sketch.*

---

## 5. User Stories & Acceptance Criteria

### User Story 1: Registering a Monitor
**As a** device administrator,  
**I want to** create a new monitor for my device,  
**So that** the system knows to track its status.

**Acceptance Criteria:**
- [ ] The API accepts a `POST /monitors` request.
- [ ] Input: `{"id": "device-123", "timeout": 60, "alert_email": "admin@critmon.com"}`.
- [ ] The system starts a countdown timer for 60 seconds associated with `device-123`.
- [ ] Response: `201 Created` with a confirmation message.

### User Story 2: The Heartbeat (Reset)
**As a** remote device,  
**I want to** send a signal to the server,  
**So that** my timer is reset and no alert is sent.

**Acceptance Criteria:**
- [ ] The API accepts a `POST /monitors/{id}/heartbeat` request.
- [ ] If the ID exists and the timer has NOT expired:
    - [ ] Restart the countdown from the beginning (e.g., reset to 60 seconds).
    - [ ] Return `200 OK`.
- [ ] If the ID does not exist:
    - [ ] Return `404 Not Found`.

### User Story 3: The Alert (Failure State)
**As a** support engineer,  
**I want to** be notified immediately if a device stops sending heartbeats,  
**So that** I can deploy a repair team.

**Acceptance Criteria:**
- [ ] If the timer for `device-123` reaches 0 seconds (no heartbeat received):
    - [ ] The system must internally "fire" an alert.
    - [ ] **Implementation:** For this project, simply `console.log` a JSON object: `{"ALERT": "Device device-123 is down!", "time": <timestamp>}`. (Or simulate sending an email).
    - [ ] The monitor status changes to `down`.

---

## 6. Bonus User Story (The "Snooze" Button)
**As a** maintenance technician,  
**I want to** pause monitoring while I am repairing a device,  
**So that** I don't trigger false alarms.

**Acceptance Criteria:**
- [ ] Create a `POST /monitors/{id}/pause` endpoint.
- [ ] When called, the timer stops completely. No alerts will fire.
- [ ] Calling the heartbeat endpoint again automatically "un-pauses" the monitor and restarts the timer.

---

## 7. The "Developer's Choice" Challenge
We value engineers who look for "what's missing."

**Task:** Identify **one** additional feature that makes this system more robust or user-friendly.
1.  **Implement it.**
2.  **Document it:** Explain *why* you added it in your README.
    * *Examples:* A `GET /status` endpoint to see all active monitors; a "max retries" feature before alerting; persistence (saving monitors to a file so they survive a server restart).

---

## 8. Documentation Requirements
Your repository **must** include a `README.md` (this file, updated) covering:

1.  **Architecture Diagram:** Your visual design from Section 4.
2.  **Setup Instructions:** How to install dependencies and run your server locally.
3.  **API Documentation:** A clear list of endpoints, methods, and example payloads.
4.  **The Developer's Choice:** Explanation of your added feature.

---

## 9. Demo Preparation
Be prepared for a 30-minute interview where you will:
1.  **Walk through your Diagram:** Explain your logic before showing the code.
2.  **Live Demo:**
    * Start your server.
    * Create a monitor with a short timeout (e.g., 10 seconds).
    * Send a heartbeat at 5 seconds (show it doesn't alert).
    * Wait 11 seconds (show the alert firing in the console).
3.  **Q&A:** Be ready to answer: "How did you handle the timers in memory?" and "What happens if the server crashes?"
