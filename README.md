# Seismic Poster

<p align="justify">
This project implements a distributed <b>serverless system</b> for monitoring and automatically publishing information about seismic activity in Italy — with a focus on the Umbria region.  
The solution uses <b>Kubernetes</b> and <b>OpenFaaS</b> to deploy Python functions that interact with the <b>INGV API</b> and the <b>X (Twitter) Developer API</b> to post real-time earthquake updates.
</p>

---

## What it does
<p align="justify">
The system automatically collects seismic data from official INGV sources and publishes summaries on X:
</p>

- 🕗 **Twice a day** (08:00 and 20:00 local time): posts information about the strongest earthquake recorded in the previous 12 hours.  
- 🚨 **Every 15 minutes**: checks for earthquakes with a magnitude ≥ 4.5 and posts an alert if new intense activity is detected.

<p align="justify">
Both operations are completely automated through Kubernetes <b>CronJobs</b> that periodically trigger two OpenFaaS functions:
</p>

- `poster-info`: posts the most significant seismic event in the last 12 hours.  
- `poster-alert`: detects and publishes alerts for strong quakes (M ≥ 4.5).

---

## Architecture
<p align="justify">
The project is deployed on a local or cloud-based <b>Kubernetes cluster</b> running OpenFaaS.  
Each function is containerized via Docker and exposed as a serverless endpoint in the <b>openfaas-fn</b> namespace.
</p>

Main components:
- **OpenFaaS functions** (`poster-info`, `poster-alert`) implemented in Python (Flask templates).  
- **Kubernetes CronJobs** that periodically invoke the functions.  
- **Kubernetes Secret** to securely store X API credentials (API Key, API Secret, Access Token, Access Secret).  
- **Cyclic scheduling**: `cronjob-info` (every 12 hours) and `cronjob-alert` (every 15 minutes).  

---

## Technologies
- **Kubernetes**
- **OpenFaaS**
- **Docker**
- **Python 3**
- **X (Twitter) API**
- **INGV API**

---

## Setup Overview
1. Create a **Twitter Developer** account and generate API keys (OAuth 1.0a).  
2. Deploy an **OpenFaaS** instance on Kubernetes (e.g., via Arkade or Kubespray).  
3. Create a **Kubernetes Secret** containing the X API credentials.  
4. Build and deploy the two OpenFaaS functions:
   - `poster-info`
   - `poster-alert`
5. Create and apply the **CronJobs** to schedule function invocations.  
6. Use `kubectl port-forward` to expose the OpenFaaS gateway locally.  
7. Verify function status and logs via:
   ```bash
   faas-cli list
   faas-cli logs poster-info
   faas-cli logs poster-alert
