# Repository Description: agent-sop

## Repository Overview

The `agent-sop` repository is a collection of Standard Operating Procedures (SOPs) designed for AI agents to follow when performing common operational tasks. This documentation-only repository provides structured, detailed procedures for codebase analysis, AWS infrastructure documentation, and incident postmortem analysis. The SOPs are written in a machine-readable format with explicit constraints and steps that ensure consistent, high-quality execution of complex tasks by AI assistants.

The repository contains three core SOPs that cover the complete lifecycle of system documentation and incident management. These procedures are designed to be consumed directly by AI agents, enabling automated generation of documentation, architectural diagrams, and incident analysis reports based on verified data sources.

## Software Components

### 1. Codebase Summary SOP
**File:** `codebase-summary.sop.md`  
**Purpose:** Analyzes codebases and generates comprehensive documentation including structured metadata files that describe system architecture, components, interfaces, and workflows. Can create targeted documentation files like AGENTS.md, README.md, CONTRIBUTING.md, or generate a complete documentation ecosystem.  
**Deployment Method:** Designed for consumption by AI agents as procedural instructions  
**Dependencies:** None (documentation-only)  
**Inputs/Outputs:** Takes parameters such as output directory, consolidation options, and codebase path; outputs structured documentation files with Mermaid diagrams  

### 2. AWS Architecture Documentation SOP
**File:** `aws-architecture-documentation.sop.md`  
**Purpose:** Documents AWS infrastructure architecture by generating comprehensive, verified documentation including visual diagrams and detailed component descriptions. Combines live AWS data with codebase analysis to create accurate architectural documentation.  
**Deployment Method:** Designed for consumption by AI agents as procedural instructions  
**Dependencies:** None (documentation-only)  
**Inputs/Outputs:** Takes parameters such as target service, AWS profiles, and codebase path; outputs architecture documentation with Mermaid flowcharts containing concrete AWS resource names, IDs, and ARNs  

### 3. Incident Postmortem SOP
**File:** `incident-postmortem.sop.md`  
**Purpose:** Conducts incident postmortems using verified data from communication tools (Slack), metrics, logs, and alerts. Builds a UTC timeline and performs Five Whys root cause analysis to produce blameless postmortem documents with actionable items.  
**Deployment Method:** Designed for consumption by AI agents as procedural instructions  
**Dependencies:** None (documentation-only)  
**Inputs/Outputs:** Takes parameters such as incident title, Slack channel, time interval, and alert names; outputs comprehensive postmortem documents with validated timelines and action items  

## Infrastructure Overview

This is a documentation-only repository with no deployment infrastructure. There are no CI/CD pipelines, build processes, or runtime environments associated with this repository. The repository consists purely of Markdown files containing procedural instructions for AI agents.

The SOPs themselves describe processes that would interact with various infrastructure components when executed by AI agents:
- AWS APIs for infrastructure discovery
- Git repositories for codebase analysis
- Monitoring systems (CloudWatch, Datadog, etc.) for metrics collection
- Communication platforms (Slack) for incident investigation
- Version control systems for pull request creation

However, the repository itself requires no specialized infrastructure beyond standard Git repository hosting.