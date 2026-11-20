> **Skill:** cloud-data-systems (AWS managed services — AI agent infrastructure) · **Source:** Baruch CIS 9760 guest-speaker event (an AWS specialist) · **Mode:** Documentation study · **Date:** ~2026-11
> Companion portfolio note (the framing + Gen-2 use): [`portfolio-defense/agent-application-pipeline_gen2-reference.md`](../../portfolio-defense/agent-application-pipeline_gen2-reference.md) · index: [cloud-data-systems README](../README.md)
> *Anonymized by request — the speaker is referred to only as "an AWS specialist." This note captures the AWS *service* facts; the portfolio framing lives in the companion note.*

# Amazon Bedrock AgentCore — managed infrastructure for running AI agents (AWS)

**Why 🛡️ and why it's cloud:** this is an AWS managed service, in the same family as the EC2 / S3 /
EMR / Kinesis work in this folder — it's the cloud-infrastructure answer to "where does an AI agent
actually *run* in production, safely?" Pure service knowledge here; the portfolio implications are
in the companion note.

## The distinction that organizes it
- **AI agent *framework*** — the logic deciding *what the agent does and which tool it calls when*
  (e.g. Claude Agent SDK, LangGraph, CrewAI). This is application code.
- **AgentCore** — AWS *infrastructure* to run and govern such agents. It is **framework-agnostic and
  model-agnostic** (supports multiple frameworks and models), so it's not a lock-in to one stack.

Put plainly: the framework is *what to do*; AgentCore is *the managed place it runs and the controls
around it* — the cloud layer, not the decision logic.

## The components (as cloud infrastructure)
| Component | What it provides |
|---|---|
| **Runtime** | the managed execution environment the agent runs in |
| **Memory** | conversation / task context management across turns |
| **Identity** | access control — which user/agent may reach which resource |
| **Gateway** | connection to external APIs and tools |
| **Code Interpreter** | a sandbox to execute code |
| **Browser** | managed web-based actions |
| **Observability** | tracing of runs and errors |
| **Policy** | controls on agent behavior |
| **Evaluations** | measuring agent performance |

The first group (Runtime / Memory / Identity / Gateway / Code Interpreter / Browser) is the *run*
surface; the last three (Observability / Policy / Evaluations) are the *govern* surface — the part
that distinguishes "it runs" from "it runs in a company."

## Where it sits in the AWS picture I already know
Same mental model as the rest of this folder: AWS gives you managed building blocks so you don't
operate the servers yourself. EC2 = compute you manage; Lambda = event-driven compute AWS manages
(see the Kinesis note); EMR = managed Spark; **AgentCore = managed runtime + governance for agents.**
The trend is consistent — move up the stack, let AWS own the operational layer.

## Development layer — fluency
- **Can now do:** explain what AgentCore is, separate framework from infrastructure, and name the
  run-vs-govern split of its components.
- **Still shaky:** I have **not** provisioned or deployed on AgentCore — this is service knowledge
  from a talk + docs, not hands-on like EC2/S3/EMR/Kinesis were.
- **Reproduce cold?** Concept yes; hands-on no (never provisioned it).

## Defense layer — interview
- **Line:** *"AgentCore is AWS's managed infrastructure for running AI agents — framework- and
  model-agnostic — split into a run surface (runtime, memory, identity, gateway, code interpreter,
  browser) and a govern surface (observability, policy, evaluations). It's the same move as Lambda or
  EMR: let AWS own the operational layer."*
- **Follow-up risk:** "walk me through deploying on it" — I can't; I know the service shape, not the
  console flow.

## Honest limit
Service knowledge only. I will NOT present this as hands-on AWS experience the way I can for EC2, S3,
IAM, Docker/OpenSearch, EMR/Spark, and Kinesis/Lambda — those I provisioned; this I studied.
