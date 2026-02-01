# ScriptGrid Requirements

## Purpose

ScriptGrid is a **distributed script execution service**. It runs SQL and PowerShell scripts on a schedule or on demand, while coordinating multiple nodes as a single grid.

## Goals

- Provide a **simple, reliable** way to run scripts across many nodes.
- Support **cron-like scheduling** and **manual triggers**.
- Allow teams to onboard new jobs with **minimal code changes**.
- Provide **cluster coordination** so only one node runs a given task at a time.
- Enable **observability** (logs, status, execution history) for operational confidence.

## Non-Goals

- General-purpose workflow engine (use Khaos.Flow / Khaos.Pipeline for complex orchestration).
- Long-running ETL engine or distributed compute framework.
- Full multi-tenant isolation or per-tenant routing.

## Core Concept

“Grid” means multiple ScriptGrid instances working together. Nodes communicate via **SignalR** to coordinate state, send commands, and broadcast events to the cluster.

## Functional Requirements

### Script Execution

- Must execute **SQL scripts** against configured data sources.
- Must execute **PowerShell scripts** in a controlled environment.
- Each execution must capture:
	- Start/end time
	- Exit status and errors
	- Standard output/error
	- Node identifier

### Scheduling

- Must support **cron-style expressions**.
- Must support **one-off** and **recurring** schedules.
- Must allow **manual run** outside schedule.

### Task Registration

- Tasks can be registered by:
	- **Configuration** (appsettings) pointing to script files
	- **Code** (C#) for advanced tasks
- Task metadata should include:
	- Name, description
	- Script type and location
	- Schedule
	- Enabled/disabled flag

### Grid Coordination

- Nodes discover and coordinate through SignalR.
- A **registry/leader election** model should exist:
	- First node becomes leader
	- Leader maintains registry of nodes and scheduled tasks
	- Failover promotes another node automatically

### Reliability

- If a node fails mid-run, execution status must be recorded as failed or timed out.
- Jobs should not run concurrently on multiple nodes unless explicitly configured.

### Observability

- Provide logs per execution.
- Provide execution history and status per task.
- Expose health checks for node and scheduler status.

## Configuration Requirements

- Define scripts and schedules in appsettings (JSON or environment).
- Support environment overrides.
- Separate config for:
	- SQL connection strings
	- PowerShell execution policy and working directory
	- Scheduler options

## Extension Points

- Add new script runners (e.g., Bash, Python) via a plugin model.
- Custom execution policies and pre/post hooks.
- Integrate with Khaos.Flow or Khaos.Pipeline for advanced orchestration.

## Out of Scope (for initial version)

- UI for managing tasks
- Multi-tenant access control
- Distributed locking beyond the grid’s own coordination mechanism

## Open Questions

- Do we want persistent storage for schedules and execution history, or can it be in-memory + logs?
- What is the expected maximum schedule density (jobs per minute/hour)?
- Should tasks support retries and backoff policies?

## Summary

ScriptGrid should stay **small and focused**: schedule and run scripts across a coordinated cluster, with clear configuration, reliable execution, and strong observability. Complex orchestration belongs in Flow/Pipeline, but ScriptGrid provides the stable entry point for timed script execution.

