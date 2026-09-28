# Cloud IAM Attack Path Finder

A read-only tool that scans an AWS account's IAM configuration, models identities and permissions as a graph, finds privilege-escalation paths, scores them by risk, and suggests least-privilege fixes.

> **Status:** in progress. Sections marked TODO are filled in as each stage is completed.

## Scenario

The lab is built around a real deployed service: [RPCChat](https://github.com/Aditya-Ampar/RPCChat), a hardened Go RPC chat service (see its VAPT report and release history), running on an EC2 instance.

Application hardening does not cover the cloud permissions around the app. The question this tool answers:

> If an attacker gets a foothold on the RPCChat server, or steals a developer's credentials, what can they reach in this AWS account, and by which path?

The lab adds resources a chat service would plausibly use (an S3 bucket for `chat.db` backups and logs) plus deliberately misconfigured IAM roles and users. RPCChat itself does not currently call these AWS services; they exist to make the environment realistic.

## What it does

1. **Collect**: read-only boto3 enumeration of users, roles, policies, trust relationships and instance profiles
2. **Model**: build a graph (NetworkX) of principals and resources with edges such as *can assume*, *can pass role to*, *can read*
3. **Find paths**: search from low-privilege entry points to high-value targets (admin role, backup bucket)
4. **Score**: a deliberately simple weighted model (wildcards, trust-boundary crossings, blast radius)
5. **Remediate**: suggest scoped-down policies, apply them, re-scan, and diff before/after
6. **Detect**: map each attack path to the CloudTrail events that would reveal it

## Scope and non-goals

- Read-only analysis of an account the author owns. It does not exploit anything.
- No AI/LLM component.
- Not a replacement for mature tools such as PMapper, Cloudsplaining or Prowler. This is a focused implementation built to understand how attack-path analysis works.

## Repository layout

| Path | Purpose |
|---|---|
| `infra/` | Terraform for the lab environment |
| `collector/` | boto3 enumeration |
| `analyzer/` | graph building, path finding, scoring, remediation |
| `detections/` | CloudTrail event mappings and detection queries |
| `visualizer/` | attack-path graph visualization |
| `reports/` | findings and before/after reports |
| `docs/` | design notes and decisions |
| `data/sample/` | sanitized example snapshots |

## Findings

TODO: summary of the attack paths found in the lab and the before/after result.

## Known limitations

TODO: filled in honestly as the project is built.

## Setup

TODO: prerequisites, Terraform apply/destroy steps, how to run the collector and analyzer.
