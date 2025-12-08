# Incident Postmortem

## Overview

This SOP guides the creation of comprehensive incident postmortems by gathering and analyzing data from multiple sources including communication tools (Slack), metrics, logs, and alerts. The postmortem builds a verified timeline in UTC and conducts root cause analysis using the Five Whys methodology. All findings must be validated against real data sources—no fabrication or inference is permitted.

The SOP produces a blameless postmortem document that focuses on systemic improvements rather than individual blame, with concrete action items to prevent recurrence. At the end of execution, the agent MUST create a pull request with the postmortem document in the specified target repository.

## Parameters

- **incident_title** (required): Brief title describing the incident (e.g., "Production API Outage - Payment Service")
- **slack_channel** (required): Channel name to search for incident communications (e.g., "#incident-2024-01-15-api-outage")
- **time_interval** (required): Start and end time in UTC of the incident window (e.g., "2024-01-15T14:30:00Z to 2024-01-15T16:45:00Z")
- **relevant_alerts** (required): List of alert names/IDs to investigate (e.g., ["HighErrorRate-PaymentService", "P95LatencyExceeded"])
- **metrics_sources** (optional): Monitoring systems to query - CloudWatch, Datadog, Prometheus, Grafana endpoints
- **log_sources** (optional): Log systems to analyze - CloudWatch Logs, ELK, Splunk, Loki references
- **output_path** (optional, default: "docs/postmortems"): Directory path where the postmortem document will be saved
- **target_repository** (required): Repository URL or path where the postmortem PR should be created
- **pr_branch** (optional, default: "docs/postmortem-{incident-slug}-{timestamp}"): Branch name for the PR
- **pr_reviewers** (optional): List of GitHub/GitLab usernames to request review from

**Constraints for parameter acquisition:**
- You MUST ask for all required parameters upfront in a single prompt
- You MUST validate that the time_interval is in valid UTC format
- You MUST confirm the Slack channel exists and is accessible before proceeding
- You MUST validate that the target_repository is accessible and you have write permissions
- You MUST clarify ambiguous alert names if multiple matches are found
- You MUST support multiple input methods for time_interval:
  - ISO 8601 format: "2024-01-15T14:30:00Z to 2024-01-15T16:45:00Z"
  - Relative format: "last 4 hours", "yesterday 2pm-6pm UTC"
  - Natural language: "January 15th from 2:30pm to 4:45pm UTC"
- You MUST convert all time inputs to UTC and confirm with the user

## Steps

### 1. Gather User Inputs

Collect all required parameters and validate access to data sources.

**Constraints:**
- You MUST ask for all required parameters upfront in a single prompt
- You MUST validate that time_interval values are parseable and convert to UTC
- You MUST verify Slack channel accessibility using available tools
- You MUST verify target_repository accessibility and write permissions
- You MUST confirm the list of alerts to investigate
- You MUST identify available metrics and log sources
- You MUST NOT proceed until all required parameters are confirmed
- You MUST create the output_path directory if it doesn't exist

### 2. Search Communications

Query Slack and other communication tools for incident-related discussions.

**Constraints:**
- You MUST use Slack search tools to query the specified channel within the time_interval
- You MUST expand the search window by 30 minutes before and after the incident to capture pre-incident signals and resolution confirmation
- You MUST capture the following from communications:
  - Initial incident detection/report (who noticed, when, how)
  - Key decisions made during incident response
  - Actions taken and their outcomes
  - Escalations and participant involvement
  - Resolution steps and confirmation
  - Customer/stakeholder communications sent
- You MUST extract exact timestamps (converted to UTC) for each significant event
- You MUST identify key participants and their roles during the incident
- You MUST preserve exact quotes for critical communications
- You MUST note any communication gaps or unclear timelines
- You MUST NOT fabricate or infer communications that weren't found

### 3. Collect Metrics Data

Pull relevant metrics from monitoring systems to understand the incident's technical impact.

**Constraints:**
- You MUST query metrics for the incident time_interval plus buffer (30 min before/after)
- You MUST collect metrics related to:
  - Error rates and error types
  - Latency (p50, p95, p99)
  - Request/transaction volumes
  - Resource utilization (CPU, memory, disk, network)
  - Queue depths and processing rates
  - Database connection pools and query latency
  - Cache hit/miss rates
- You MUST capture baseline metrics from before the incident for comparison
- You MUST identify the exact time when metrics deviated from normal
- You MUST document the peak impact values with timestamps
- You MUST identify when metrics returned to normal levels
- You MUST note which metrics were unavailable and why
- You MUST NOT invent metric values—use "unknown (metric not available)" if data cannot be retrieved

### 4. Analyze Alerts and Logs

Examine triggered alerts and log entries to understand the incident sequence.

**Constraints:**
- You MUST retrieve all alerts from the relevant_alerts list that fired during the time_interval
- You MUST capture for each alert:
  - Alert name and ID
  - Trigger time (UTC)
  - Threshold that was breached
  - Alert severity/priority
  - Time to acknowledgment
  - Resolution time
- You MUST query logs from specified log_sources for:
  - Error messages and stack traces
  - Warning patterns leading up to the incident
  - System state changes (deployments, config changes, restarts)
  - Authentication/authorization failures
  - External service failures or timeouts
- You MUST correlate log entries with alert triggers and metric anomalies
- You MUST identify the earliest indicator of the problem in logs
- You MUST document any relevant log entries that preceded the first alert
- You MUST note which logs were unavailable or incomplete
- You MUST NOT fabricate log entries—quote exact log messages with timestamps

### 5. Build UTC Timeline

Construct a comprehensive chronological timeline of events from all data sources.

**Constraints:**
- You MUST present all times in UTC format (YYYY-MM-DD HH:MM:SS UTC)
- You MUST include the source for each timeline entry (Slack, metrics, logs, alerts)
- You MUST order events chronologically, resolving conflicts between sources
- You MUST include these timeline phases:
  - **Pre-incident**: Any warning signs or contributing events
  - **Detection**: First alert or human detection
  - **Response**: When response began, who was involved
  - **Investigation**: Key diagnostic steps and findings
  - **Mitigation**: Actions taken to reduce impact
  - **Resolution**: When service was restored
  - **Post-resolution**: Verification and cleanup activities
- You MUST flag any timeline gaps where events are unclear or missing
- You MUST cross-reference timeline entries across multiple sources when possible
- You MUST note confidence level for entries based on single vs. multiple sources
- You MUST NOT fill gaps with assumed or fabricated events

### 6. Conduct Five Whys Analysis

Perform root cause analysis using the Five Whys methodology with evidence requirements.

**Constraints:**
- You MUST start with the incident symptom as the first "Why"
- You MUST ask "Why did this happen?" at least 5 times to reach root cause
- You MUST provide evidence for each answer from:
  - Code references (file paths, line numbers, commits)
  - Configuration values (actual settings from systems)
  - Metrics data (specific values and timestamps)
  - Log entries (exact quotes with timestamps)
  - Slack communications (quotes from incident discussion)
- You MUST NOT accept assumptions without verification
- You MUST explore multiple branches if the cause has multiple contributing factors
- You MUST identify systemic issues, not just proximate causes
- You MUST distinguish between:
  - **Root cause**: The fundamental reason the incident occurred
  - **Contributing factors**: Conditions that enabled or worsened the incident
  - **Trigger**: The immediate event that initiated the incident
- You MUST validate each "Why" answer against available evidence
- If evidence is insufficient, you MUST note "Unable to verify - requires further investigation"

### 7. Validate Findings

Cross-reference all findings against original data sources to ensure accuracy.

**Constraints:**
- You MUST verify every factual claim against its original source
- You MUST re-check all timestamps for accuracy and UTC consistency
- You MUST confirm metric values match what was retrieved from monitoring systems
- You MUST verify log quotes are exact matches from the original logs
- You MUST confirm alert details match the actual alert configurations
- You MUST validate that the timeline is internally consistent (no impossible sequences)
- You MUST verify Five Whys conclusions are supported by cited evidence
- You MUST check for contradictions between different data sources
- You MUST document any discrepancies found and how they were resolved
- You MUST NOT proceed if critical findings cannot be verified

### 8. Generate Postmortem Document

Create the comprehensive postmortem document with all required sections.

**Constraints:**
- You MUST generate a document with the following sections in order:

**8.1 Introduction**
- Incident title and unique identifier
- Date and duration (in UTC)
- Severity level and classification
- Services/systems affected
- Incident commander and key responders
- One-paragraph executive summary

**8.2 Business Impact**
- Customer-facing impact (what users experienced)
- Duration of customer impact
- Number of affected users/transactions (if measurable)
- Revenue impact (if calculable)
- SLA/SLO breaches with specific metrics
- Reputational impact and customer communications sent
- Internal impact (productivity, other dependent systems)

**8.3 Timeline (UTC)**
- Complete chronological timeline from Step 5
- Each entry must include: timestamp, event description, source
- Use table or structured list format for clarity
- Highlight critical decision points
- Mark detection, mitigation, and resolution times clearly

**8.4 Where We Got Lucky**
- Factors that limited the impact but were not by design
- Near-misses that could have made the incident worse
- Fortunate timing or circumstances
- Each item must explain what could have been worse

**8.5 What Went Well**
- Effective incident response actions
- Tools or processes that helped detection or resolution
- Positive team behaviors and collaboration
- Successful mitigations or fallbacks that worked as designed
- Each item must cite specific evidence from the timeline

**8.6 What Went Wrong**
- Failures in detection, prevention, or response
- Process breakdowns or gaps
- Tool limitations or failures
- Communication issues
- Each item must be factual and cite evidence, not assign blame

**8.7 Five Whys Root Cause Analysis**
- Present the Five Whys analysis from Step 6
- Show the chain of causation clearly
- Include evidence citations for each answer
- Identify root cause(s) and contributing factors
- Include diagram if multiple branches exist

**8.8 What We Learned**
- Key insights about our systems, processes, or organization
- Gaps in monitoring, documentation, or knowledge identified
- Assumptions that were proven wrong
- New understanding gained about system behavior

**8.9 Action Items**
- Each action item MUST include:
  - Unique identifier (e.g., PI-2024-001-01)
  - Clear description of the work
  - Owner (team or individual)
  - Priority (P0-Critical, P1-High, P2-Medium, P3-Low)
  - Due date
  - Success criteria (how we know it's done)
  - Category (Prevention, Detection, Response, Recovery)
- You MUST include actions addressing:
  - Root cause remediation
  - Detection improvements (catch it earlier)
  - Response improvements (resolve it faster)
  - Prevention measures (stop it from happening)
- You MUST NOT include vague actions like "improve monitoring"—be specific

### 9. Validate Postmortem Document

Review the generated document for completeness, accuracy, and hallucinations.

**Constraints:**
- You MUST verify all required sections are present and populated
- You MUST confirm all timeline entries have UTC timestamps and source references
- You MUST validate that Five Whys conclusions are supported by cited evidence
- You MUST check that action items are specific, assignable, and have measurable success criteria
- You MUST perform hallucination check:
  - Re-verify every claim, metric, timestamp, and finding against original data sources
  - Flag any information that cannot be traced back to Slack messages, logs, metrics, or alerts
  - Remove or mark as "unverified" any claims without source evidence
  - Ensure no fabricated quotes, timestamps, or metric values exist
- You MUST verify internal consistency:
  - Timeline events are in chronological order
  - Durations are mathematically correct
  - Metric values are plausible and consistent
- You MUST check for completeness:
  - All referenced alerts are accounted for
  - No significant time gaps without explanation
  - All key participants mentioned in timeline appear in responder list
- You MUST present a validation summary to the user including:
  - Sections completed
  - Number of timeline entries with sources
  - Number of action items created
  - Any items flagged as unverified or requiring follow-up
  - Request user confirmation before proceeding to PR creation

### 10. Create Pull Request

Create a pull request with the postmortem document in the target repository.

**Constraints:**
- You MUST generate a descriptive filename from the incident title (slugified, e.g., `2024-01-15-payment-service-outage.md`)
- You MUST save the postmortem document to the output_path directory in the target_repository
- You MUST create a new branch using the pr_branch parameter (or default: "docs/postmortem-{incident-slug}-{timestamp}")
- You MUST commit the postmortem document with a signed commit and descriptive message including:
  - Incident date and title
  - Brief summary of root cause
  - Number of action items
- You MUST create a pull request with:
  - Title: "docs: Add postmortem for {incident_title}"
  - Description including:
    - Incident summary (date, duration, severity)
    - Services affected
    - Brief root cause summary
    - Number of action items (by priority)
    - Link to incident Slack channel (if appropriate)
    - Checklist for reviewers
  - Labels: ["postmortem", "documentation"]
  - Reviewers: Use pr_reviewers parameter if provided
- You MUST provide the PR URL and number to the user
- You MUST NOT merge the PR automatically—it must be reviewed and merged manually
- You MUST handle errors gracefully and provide clear error messages if PR creation fails

## Examples

### Example Input
```
incident_title: "Production Database Connection Pool Exhaustion"
slack_channel: "#incident-2024-01-15-db-connections"
time_interval: "2024-01-15T14:30:00Z to 2024-01-15T16:45:00Z"
relevant_alerts: ["RDS-ConnectionCount-High", "API-P99-Latency-Critical", "HealthCheck-Failures"]
metrics_sources: ["CloudWatch", "Datadog"]
log_sources: ["CloudWatch Logs: /aws/ecs/api-service", "Datadog: service:api-gateway"]
output_path: "docs/postmortems"
target_repository: "https://github.com/company/platform-docs"
pr_reviewers: ["oncall-lead", "platform-team"]
```

### Example Output (Sections)

#### Introduction
```markdown
# Postmortem: Production Database Connection Pool Exhaustion

**Incident ID:** INC-2024-0115-001
**Date:** 2024-01-15
**Duration:** 2 hours 15 minutes (14:30 - 16:45 UTC)
**Severity:** SEV-1 (Critical)
**Services Affected:** API Gateway, Payment Service, Order Service
**Incident Commander:** @jane-doe
**Key Responders:** @john-smith (DBA), @alex-chen (Platform), @maria-garcia (On-call)

## Executive Summary

On January 15, 2024, the production API experienced degraded performance and partial outage 
due to database connection pool exhaustion. The incident was triggered by a combination of 
increased traffic from a marketing campaign and a connection leak introduced in deployment 
v2.3.45. Customer-facing impact lasted 1 hour 45 minutes, affecting approximately 15,000 
users with failed or slow transactions. The incident was resolved by rolling back to v2.3.44 
and manually recycling database connections.
```

#### Timeline Example
```markdown
## Timeline (UTC)

| Time (UTC) | Event | Source |
|------------|-------|--------|
| 2024-01-15 14:15:00 | Marketing campaign email sent, traffic begins increasing | Slack: @marketing-bot |
| 2024-01-15 14:28:00 | Connection pool utilization reaches 75% (baseline: 40%) | CloudWatch: RDS-ConnectionCount |
| 2024-01-15 14:30:00 | First P99 latency alert triggers (>2s threshold) | Alert: API-P99-Latency-Critical |
| 2024-01-15 14:32:00 | @maria-garcia acknowledges alert, begins investigation | Slack: "#incident-2024-01-15-db-connections" |
| 2024-01-15 14:35:00 | Connection pool reaches 95%, new connections failing | CloudWatch Logs: "Connection pool exhausted" |
| 2024-01-15 14:38:00 | Health check failures begin, ECS tasks marked unhealthy | Alert: HealthCheck-Failures |
| 2024-01-15 14:42:00 | Incident escalated to SEV-1, @jane-doe assumes IC role | Slack: "@jane-doe I'm taking IC" |
| 2024-01-15 14:55:00 | Root cause identified: connection leak in v2.3.45 | Slack: "@john-smith found leak in ConnectionManager.java:142" |
| 2024-01-15 15:10:00 | Decision to rollback to v2.3.44 | Slack: "IC decision: rolling back" |
| 2024-01-15 15:25:00 | Rollback complete, new connections succeeding | Deployment logs: v2.3.44 deployed |
| 2024-01-15 15:45:00 | Connection pool draining, latency improving | CloudWatch: connections dropping |
| 2024-01-15 16:30:00 | All metrics return to baseline | CloudWatch: all metrics green |
| 2024-01-15 16:45:00 | Incident declared resolved | Slack: "IC: incident resolved" |
```

#### Five Whys Example
```markdown
## Five Whys Root Cause Analysis

**Why #1: Why did the API experience degraded performance?**
- Answer: Database connection pool was exhausted, causing new requests to timeout
- Evidence: CloudWatch logs show "Connection pool exhausted" at 14:35 UTC; connection count metric at 100% (500/500)

**Why #2: Why was the connection pool exhausted?**
- Answer: Connections were not being returned to the pool after use
- Evidence: Connection checkout rate >> checkin rate in Datadog metrics; pool growth linear without release

**Why #3: Why were connections not being returned?**
- Answer: A code change in v2.3.45 introduced a connection leak in error handling path
- Evidence: `ConnectionManager.java:142` - missing `finally` block for connection close; commit abc123 on 2024-01-14

**Why #4: Why wasn't this caught before production?**
- Answer: Integration tests don't run long enough to exhaust pool; no connection leak detection in CI
- Evidence: CI test suite completes in 8 minutes; leak manifests after ~30 minutes under load

**Why #5: Why don't we have connection leak detection?**
- Answer: Connection pool monitoring only alerts on absolute count, not on leak patterns (checkout without checkin)
- Evidence: Alert configuration in Datadog only checks `pool.active > 450`, no rate-of-change alert

**Root Cause:** Missing connection release in error handling path, combined with inadequate testing for resource leaks and insufficient monitoring for leak patterns.

**Contributing Factors:**
- Marketing campaign increased traffic 3x, accelerating pool exhaustion
- Recent reduction in connection pool timeout (30s → 10s) reduced time-to-impact
```

#### Action Items Example
```markdown
## Action Items

| ID | Description | Owner | Priority | Due Date | Success Criteria | Category |
|----|-------------|-------|----------|----------|------------------|----------|
| PI-2024-0115-01 | Fix connection leak in ConnectionManager.java error handling | @backend-team | P0 | 2024-01-16 | Code merged with test coverage for error path | Prevention |
| PI-2024-0115-02 | Add connection leak detection alert (checkout rate >> checkin rate for >5min) | @platform-team | P1 | 2024-01-22 | Alert triggers in staging with simulated leak | Detection |
| PI-2024-0115-03 | Add long-running integration test (1hr) with connection pool monitoring | @qa-team | P1 | 2024-01-29 | Test runs nightly, fails if pool doesn't stabilize | Prevention |
| PI-2024-0115-04 | Document connection pool configuration and troubleshooting runbook | @platform-team | P2 | 2024-02-05 | Runbook published and linked from alert | Response |
| PI-2024-0115-05 | Implement circuit breaker for database connections | @backend-team | P2 | 2024-02-12 | Circuit breaker activates when pool >80%, tested in staging | Recovery |
```

### Example Output Structure
```
docs/postmortems/
└── 2024-01-15-database-connection-pool-exhaustion.md
```

### Example Validation Summary
```
## Postmortem Validation Summary

✅ All 9 required sections present and populated
✅ 13 timeline entries with UTC timestamps and source references
✅ 5 action items created (1 P0, 2 P1, 2 P2)
✅ Five Whys analysis complete with evidence for each level
✅ All metric values verified against CloudWatch/Datadog
✅ All Slack quotes verified against channel messages

⚠️ Items requiring follow-up:
- Connection pool timeout change date could not be verified (marked as approximate)
- Exact user impact count (15,000) based on estimate from traffic data

Ready to create PR? [Confirm to proceed]
```

## Troubleshooting

### Missing Communication Data
If Slack search returns no results or incomplete data:
- Verify the channel name is correct and accessible
- Check if the bot/integration has access to the channel
- Try expanding the time window
- Search alternative channels where discussion may have occurred
- Document communication gaps explicitly in the postmortem
- Consider interviewing participants to fill gaps (note as "verbal account" in timeline)

### Incomplete Metrics Data
If metrics cannot be retrieved for the incident window:
- Check metric retention policies (some metrics expire)
- Verify correct metric names and dimensions
- Try alternative metrics that might show similar impact
- Document which metrics were unavailable and why
- Use "unknown (metric not available)" for any values that cannot be retrieved
- Do NOT estimate or fabricate metric values

### Alert Data Issues
If alert history is incomplete:
- Check alert retention settings in monitoring system
- Look for alert notifications in Slack/email as backup source
- Verify alert names match exactly (case-sensitive)
- Document any alerts that could not be retrieved
- Note if alerts should have fired but didn't (detection gap)

### Cross-Referencing Conflicts
If different sources show conflicting information:
- Note the discrepancy explicitly in the timeline
- Prefer system-generated timestamps over human-reported times
- For factual conflicts, document both versions and which was used
- Flag unresolved conflicts for follow-up investigation
- Do NOT silently choose one version—transparency is critical

### Five Whys Dead Ends
If the Five Whys analysis cannot be completed:
- Document how far the analysis could go with evidence
- Clearly state where evidence was insufficient
- Create an action item to investigate further
- Do NOT make up root causes—"unknown" is acceptable
- Consider if more data sources need to be added for future incidents

### Target Repository Access Issues
If PR cannot be created in target repository:
- Verify repository URL is correct
- Check authentication and permissions
- Ensure the branch name doesn't already exist
- Try creating the branch manually first
- Save the postmortem document locally and provide manual instructions

### Hallucination Detection
If validation reveals potential hallucinations:
- Re-query the original data source to verify
- Remove any information that cannot be verified
- Mark uncertain information as "unverified - requires confirmation"
- Do NOT include plausible-sounding but unverified details
- When in doubt, leave it out and note the gap

