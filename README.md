# CeApiProxy (WSO2 Micro Integrator ESB Proxy)

A WSO2 Micro Integrator (MI) project that exposes a simple **ESB-style API gateway** on your local machine and proxies the Department of Computer Engineering (UoP) public APIs.

Base URL (local): `http://localhost:8290/ce`

## What this proxy provides

These resources are exposed (GET):

- `/ce/students` → proxies Students API and returns a normalized list
- `/ce/projects` → proxies Projects API and returns a normalized list
- `/ce/staff` → proxies Staff API and returns a normalized list
- `/ce/events` → proxies Events API and returns a normalized list
- `/ce/news` → proxies News API and returns a normalized list/shape (via Data Mapper)
- `/ce/publications` → proxies Publications API and returns data (via Data Mapper)

Each resource typically does:
1) **Call** upstream endpoint  
2) Optional **Script** mediator to reshape JSON (e.g., object → array)  
3) **Data Mapper** to produce the required output JSON shape  
4) **Respond**

## Prerequisites

- **Java**: use the Java version supported by your MI runtime (commonly Java 11 or 17)
- **Maven**
- **WSO2 Micro Integrator** runtime (e.g., `wso2mi-4.5.0`)
- Recommended: **VS Code + WSO2 Micro Integrator extension**

## Run (VS Code – recommended)

1. Open this project in VS Code.
2. Ensure MI runtime is configured in the extension.
3. Run **“MI: Build and Run”**.

MI should start and show a line similar to:

- `Pass-through HTTP Listener started on ...:8290`

## Run (manual)

### 1) Build the CAR
From the project root:

```bash
mvn clean install
```

This creates a `.car` in `target/` (for example: `ceapiproxy_1.0.0.car`).

### 2) Deploy the CAR into MI runtime
Copy the `.car` file into:

`<MI_HOME>/repository/deployment/server/carbonapps/`

### 3) Start MI
From `<MI_HOME>/bin`:

- PowerShell: `.\micro-integrator.bat`
- CMD: `micro-integrator.bat`

## Test

### Browser
- `http://localhost:8290/ce/students`
- `http://localhost:8290/ce/projects`
- `http://localhost:8290/ce/staff`
- `http://localhost:8290/ce/events`
- `http://localhost:8290/ce/news`
- `http://localhost:8290/ce/publications`

### Curl (PowerShell)
```powershell
curl http://localhost:8290/ce/students
curl http://localhost:8290/ce/projects
curl http://localhost:8290/ce/staff
curl http://localhost:8290/ce/events
curl http://localhost:8290/ce/news
curl http://localhost:8290/ce/publications
```

## Useful ports

- **8290** = HTTP gateway (your API)
- **8253** = HTTPS gateway
- **9201** = internal/health listener (varies by config)

## Common issue: only some resources work

If `/students` and `/projects` work but `/staff`, `/events`, `/news`, `/publications` do not, it usually means those **artifacts were not packaged into the CAR**.

Make sure you have BOTH of these files:

- `src/main/wso2mi/artifacts/artifact.xml`  (lists APIs/endpoints to package)
- `src/main/wso2mi/resources/artifact.xml`  (lists registry resources like Data Mapper files)

Then rebuild and redeploy:

```bash
mvn clean install
```

Stop MI and remove the old `.car` from:
`<MI_HOME>/repository/deployment/server/carbonapps/`
then copy the new `.car` and start MI again.

## Repository

GitHub: `https://github.com/pradeep512/CeApiProxy`
