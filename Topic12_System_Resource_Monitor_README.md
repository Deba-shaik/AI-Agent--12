# Topic 12 - System Resource Monitor (n8n)

## Overview
System_Resource_Monitor is a published n8n workflow that checks an HTTP server metrics endpoint hourly, routes non-200 responses to Slack, and records healthy responses on the alternative branch.

## Workflow
Schedule Trigger (every 1 hour) -> Get_Server_Metrics (GET http://localhost:5678/metrics) -> Check_Server_Status (statusCode != 200)

- True (unhealthy): Send_Server_Alert (Slack channel #logistics-critical-alerts)
- False (healthy): Log_Healthy_Status (Edit Fields)

## Configuration
- Schedule Trigger: every 1 hour.
- HTTP Request: GET /metrics; include status code in response; allow non-2xx responses to be inspected.
- IF condition: {{$json.statusCode}} is not equal to numeric 200.
- Slack: sends an alert containing the HTTP status and diagnostic message.
- Healthy log: outputs server_status, http_status, message, and checked_at.

## Test evidence
### Branch 1 - Unhealthy server (real HTTP response)
The metrics request returned HTTP 404 Not Found. The IF node selected the True branch. Send_Server_Alert executed successfully and the alert appeared in Slack.

### Branch 2 - Healthy server (mocked HTTP response)
A temporary Mock_Healthy_Response node supplied statusCode 200. The IF node selected the False branch and Log_Healthy_Status output server_status = Healthy and http_status = 200. This verifies the conditional branch using a simulation, not a live healthy metrics endpoint.

## Publication
The n8n editor shows System_Resource_Monitor as Published.

## Evidence files
See Topic12_System_Resource_Monitor_Evidence.pdf for screenshots of BOTH branches, the Slack alert, healthy status output, and the published workflow.

## Safety note
The mock node is for testing only and should not replace live metrics in production. Do not share secrets or authentication credentials in screenshots or workflow exports.
