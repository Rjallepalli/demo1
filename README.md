# demo1

Project: AI Platform Capacity Optimizer — Hackathon POC

1. Project Goal

Build a working proof of concept called AI Platform Capacity Optimizer, an AI-agent-driven optimization system for an enterprise application container platform hosting approximately 3,000 stateless applications on Kubernetes.

The platform uses Knative and KEDA for application autoscaling. Applications are provisioned through a platform API called ACS API, which provides teams with Kubernetes namespaces.

The goal is to demonstrate how an AI agent can analyze workload behavior across thousands of applications, identify CPU and memory over-provisioning, anticipate workload demand, evaluate resource allocation and placement options, calculate chargeback impact, and safely execute optimizations through a simulated ACS API.

This POC must demonstrate measurable business value, genuine AI reasoning and tool usage, safe execution, and a feedback loop that verifies the outcome of each optimization.

2. Business Problem

Our application container platform hosts approximately 3,000 applications.

Knative and KEDA already handle reactive autoscaling. Knative adjusts application replicas based on workload signals such as request rate and concurrency, while KEDA supports event-driven scaling based on configured metrics.

However, autoscaling alone does not necessarily optimize the CPU and memory configuration of each replica or make platform-wide cost-aware resource decisions.

Potential inefficiencies include:

* Applications with CPU requests substantially above their sustained requirements.
* Applications with excessive memory requests.
* Applications whose average utilization is low but whose peak demand requires careful analysis.
* Workloads with predictable traffic spikes.
* Applications that could benefit from better placement based on available capacity.
* Workloads that might safely scale to zero when permitted by their application policies.
* Applications for which reducing resources would risk latency, availability, or SLO violations.

The AI agent must distinguish between safe optimization opportunities and workloads that should retain their current configuration.

3. Primary Objective

Demonstrate an AI agent that can answer this question:

“Across our 3,000 applications, which workloads can be optimized to recover CPU and memory capacity, what changes should be made, when should they be made, and what is the estimated financial impact while maintaining application SLOs?”

The agent must perform a real workflow:

1. Observe workload data.
2. Analyze historical utilization.
3. Identify workload patterns.
4. Predict potential demand using historical data and an appropriate forecasting method.
5. Generate optimization candidates.
6. Calculate resource and chargeback impact.
7. Evaluate application constraints and cluster capacity.
8. Validate recommendations against deterministic safety policies.
9. Execute approved changes through a simulated ACS API.
10. Observe post-change behavior and verify the outcome.
11. Roll back simulated changes when configured verification conditions fail.
12. Produce an auditable report of decisions, actions, risks, and outcomes.

4. Development Environment

Build the first version to run locally on an office MacBook through Cursor.

Requirements:

* Use a local simulation environment.
* Do not require access to the company’s production Kubernetes clusters.
* Do not require production credentials or secrets.
* Do not modify real ACS APIs or Kubernetes resources.
* Do not require a live Kubernetes cluster for the initial demo.
* Provide deterministic synthetic workload data and repeatable scenarios.
* Keep dependencies lightweight and document installation instructions.
* Make the project runnable using a small number of documented terminal commands.
* Keep the AI provider configurable through environment variables.
* Never commit API keys or credentials.
* If an approved AI model/API is unavailable, support a clearly labeled simulation mode for testing the workflow.

Before choosing dependencies, inspect the available development environment and propose a simple implementation that is practical on macOS.

5. Simulated Platform Inventory

Generate a synthetic inventory of 3,000 applications.

Every application should have a stable application ID and realistic, varied characteristics.

Required application attributes:

* Application ID and name.
* Owning team.
* Environment and application criticality.
* Current CPU request in vCPU.
* Current memory request in Gi.
* CPU and memory limits, where applicable.
* Current replica count.
* Minimum and maximum replicas.
* Current autoscaling configuration.
* Historical CPU utilization.
* Historical memory utilization.
* Historical RPS or concurrency.
* Latency percentiles and error rate.
* SLO targets.
* Startup or scale-up delay.
* Workload classification.
* Optimization eligibility.
* Resource-change restrictions.
* Current node-pool placement.
* Current simulated hourly chargeback.

Use reproducible random generation with a configurable seed.

Create a configurable population of workload archetypes. Use the following initial distribution as a synthetic starting assumption:

* 35% appropriately sized workloads.
* 25% primarily CPU over-provisioned.
* 15% primarily memory over-provisioned.
* 10% over-provisioned in both CPU and memory.
* 10% workloads with predictable demand spikes.
* 5% irregular or bursty workloads.

These are synthetic assumptions, not claims about the actual company platform.

The generator must ensure that utilization data, resource requests, spike patterns, and application policies are logically consistent.

Do not label an application as over-provisioned solely because its average CPU utilization is low. Generate historical time series with meaningful percentiles, peaks, and workload behavior.

6. Workload Scenarios

Support at least five repeatable scenarios.

Scenario A: Normal Operation

Applications run within their normal utilization patterns. The agent analyzes the fleet and finds potential resource inefficiencies.

Scenario B: Predictable Traffic Spike

Some applications experience recurring traffic increases at predictable times.

The agent analyzes historical patterns, forecasts upcoming demand, and recommends appropriate configuration or pre-warming policies when justified by startup latency or SLO requirements.

Knative/KEDA remain responsible for runtime autoscaling.

Scenario C: Sudden Traffic Spike

An application experiences an unexpected increase in RPS.

The agent should avoid assuming that every spike is predictable. It should assess the available evidence and recommend retaining sufficient capacity or relying on existing autoscaling when prediction confidence is insufficient.

Scenario D: Cluster Capacity Pressure

Multiple node pools have different available CPU and memory capacities.

The agent evaluates workload placement options subject to resource availability, node affinity, workload restrictions, and policy constraints.

Scenario E: Failed Optimization and Rollback

An approved simulated optimization causes a configured latency, error-rate, or resource-safety threshold to be violated.

The system detects the failed verification, records the result, and restores the previous configuration in the simulation.

Every scenario must be reproducible.

7. AI Agent Responsibilities

Build a genuine tool-using AI agent. The language model must orchestrate relevant tools rather than simply return a fixed recommendation.

The agent should have access to tools with clear schemas and documented inputs and outputs.

Required tools:

* get_platform_inventory
* get_workload_configuration
* get_workload_metrics
* get_workload_history
* get_cluster_capacity
* analyze_workload_utilization
* forecast_workload_demand
* calculate_resource_options
* estimate_chargeback
* evaluate_placement_options
* check_platform_policy
* generate_optimization_plan
* apply_simulated_acs_change
* verify_optimization
* rollback_simulated_change
* generate_optimization_report

Use actual tool calls through the selected AI provider’s supported tool-calling mechanism. The model should decide which tools to call based on the task and results.

The agent should explain:

* What it observed.
* Which evidence supports its recommendation.
* Which tools it used.
* What assumptions it made.
* What change it proposes.
* Expected resource recovery and chargeback impact.
* Confidence and uncertainty.
* Risks and policy checks.
* Whether execution succeeded.
* Whether post-change verification passed.

The AI may interpret evidence and select among candidate strategies. Deterministic code must calculate costs, validate resource bounds, and enforce safety constraints.

Do not allow the model to invent utilization statistics, cost savings, capacity availability, or successful execution results.

If the model cannot obtain sufficient evidence, it should explain the limitation and refrain from making an unsafe change.

8. Workload Analysis and Forecasting

Implement deterministic workload analysis for CPU, memory, RPS, latency, and error rate.

Calculate at least:

* Mean utilization.
* P50 utilization.
* P95 utilization.
* P99 utilization.
* Peak utilization.
* Requested versus observed resources.
* Utilization during identified traffic spikes.
* Historical SLO violations.
* Estimated resource headroom.

Use historical time-series analysis to identify recurring demand patterns.

For the initial POC, use a simple, explainable forecasting method, such as a seasonal baseline or rolling-window trend, with a confidence or uncertainty measure.

The LLM agent should interpret the forecasting results and decide whether to recommend optimization, retain the current configuration, or request additional evidence.

Do not use the LLM as the numerical forecasting engine when deterministic time-series calculations are more appropriate.

Do not claim predictive accuracy unless it has been measured against held-out synthetic observations.

9. Resource Optimization Engine

Build a deterministic optimization engine that generates feasible candidate configurations.

Evaluate:

* CPU requests.
* Memory requests.
* CPU and memory limits.
* Replica counts and autoscaling bounds.
* Expected peak demand.
* Resource headroom.
* Workload startup characteristics.
* Application SLOs.
* Application criticality.
* Workload-specific restrictions.
* Node-pool capacity.
* Cost impact.

The engine should compare the current configuration against multiple feasible alternatives.

The optimization objective is to reduce resource allocation and estimated chargeback cost without violating defined safety constraints.

For the initial POC, implement a transparent heuristic-based optimizer. Keep the optimizer modular so a more advanced algorithm can be added later.

Never reduce CPU or memory solely to achieve a target savings percentage.

Do not confuse CPU requests with CPU consumption. Reducing a request can improve schedulability and capacity utilization, but it does not automatically reduce physical CPU usage or actual cloud spending.

10. Chargeback Cost Model

Use the following internal chargeback rates:

* 1 vCPU = $0.003345 per hour.
* 1 Gi memory = $0.002446 per hour.

Calculate resource chargeback as:

Hourly cost = (vCPU × $0.003345) + (Gi memory × $0.002446)

For a continuously allocated resource:

Monthly cost = Hourly cost × 730

Annualized cost = Hourly cost × 8,760

For workloads with varying replica counts, calculate costs using the sum of allocated CPU and memory across replicas over time.

For each proposed optimization, report:

* Current CPU and memory allocation.
* Proposed CPU and memory allocation.
* Current replica allocation assumptions.
* Proposed replica allocation assumptions.
* Current hourly chargeback.
* Proposed hourly chargeback.
* Estimated monthly chargeback reduction.
* Annualized chargeback reduction.
* CPU capacity recovered.
* Memory capacity recovered.
* Any assumptions about time spent at each configuration.

Use decimal precision for calculations and round only for display.

Clearly distinguish:

1. Potential chargeback reduction.
2. Resource capacity recovered.
3. Actual infrastructure cost savings.

The initial POC may estimate the first two. It must not claim verified cloud cost savings without evidence of actual infrastructure expenditure reduction.

Avoid double-counting resource savings across overlapping optimization categories.

11. Safety and Policy Engine

Implement an independent deterministic policy engine.

The AI must not be able to bypass it.

Required safeguards:

* Dry-run mode by default.
* Explicit approval before applying simulated changes.
* Maximum permitted CPU and memory changes per action.
* Minimum and maximum replica bounds.
* Minimum resource headroom.
* Workload criticality restrictions.
* SLO and latency thresholds.
* Memory safety margins.
* Node capacity constraints.
* Cooldown periods to prevent repeated changes.
* Rollback configuration and verification thresholds.
* Audit logs for every proposed, approved, rejected, applied, and rolled-back action.

Reject infeasible recommendations and explain why.

The model must not execute arbitrary shell commands or directly mutate Kubernetes resources.

For this POC, all changes must go through the simulated ACS API.

12. Simulated ACS API

Implement a local ACS API service that represents the company’s application platform control plane.

It should support:

* Retrieving application configuration.
* Listing applications.
* Submitting an optimization proposal.
* Validating requested configuration changes.
* Applying an approved simulated configuration update.
* Retrieving the updated configuration.
* Rolling back to a previous configuration.
* Recording action history.

The simulated ACS API must maintain application state between requests while the application is running.

Model the boundary between the AI optimizer and the platform API clearly, so the simulated implementation can later be replaced with an approved real ACS integration.

Do not assume that changing a CPU request or memory request dynamically is supported by the actual platform. Document the difference between changing a deployment template, rolling out updated pods, adjusting autoscaling settings, and selecting a node pool.

13. Cluster Capacity and Placement

Create a simulated cluster with several node pools that have different CPU and memory capacities and different utilization profiles.

Represent:

* Total CPU and memory.
* Allocatable CPU and memory.
* Requested CPU and memory.
* Available headroom.
* Node-pool labels and workload constraints.
* Workload affinity or anti-affinity restrictions.
* Placement eligibility.

Implement deterministic placement feasibility checks.

The AI agent can request placement analysis and explain the best feasible options.

Do not let the AI arbitrarily select a node that cannot accommodate the workload.

For the POC, placement decisions must be recommendations or simulated actions only. Do not claim that ordinary Knative autoscaling automatically performs AI-driven cross-node-pool placement.

14. Dashboard and User Experience

Build a simple local dashboard with an executive summary, workload explorer, AI agent activity panel, optimization review, and scenario controls.

Executive Summary

Display:

* Total applications analyzed.
* Total simulated active replicas.
* Total CPU requests.
* Total memory requests.
* Current modeled hourly and monthly chargeback.
* Number of optimization candidates.
* Proposed CPU capacity recovery.
* Proposed memory capacity recovery.
* Potential monthly and annualized chargeback reduction.
* Number of recommendations rejected by policy.
* Number of applied changes.
* Number of successful verifications.
* Number of rollbacks.

Every number must be calculated from the active dataset and current simulated state.

Workload Explorer

Allow users to select an application and inspect:

* Current configuration.
* Historical utilization.
* RPS and latency trends.
* CPU and memory percentiles.
* Forecast demand.
* AI recommendations.
* Estimated chargeback impact.
* Policy results.
* Execution and verification history.

AI Agent Activity

Display an understandable event log showing tool calls, evidence gathered, decisions, policy checks, simulated API actions, and verification results.

Do not expose private internal chain-of-thought. Show concise, evidence-based explanations and a structured decision trace.

Optimization Review

Display a sortable table of candidate optimizations, including estimated savings, resources recovered, confidence, criticality, and risk.

Support approving or rejecting recommendations individually and, where safe, running a policy-validated batch.

Scenario Controls

Allow users to start, pause, reset, and run the predefined workload scenarios.

Include a “Analyze Platform” action and an “Optimize Selected Workloads” action.

Execution must remain simulated and must require explicit approval.

15. Required Demonstration

The finished POC must support this end-to-end demo:

1. Start the local application and load 3,000 synthetic applications.
2. Display the baseline application count, resource requests, and chargeback.
3. Ask the AI agent to analyze the platform and identify cost optimization opportunities.
4. Show the agent gathering evidence through tools.
5. Inspect the top recommendations and their supporting historical metrics.
6. Ask the agent to explain why a particular application is over-provisioned.
7. Trigger a predictable traffic-spike scenario.
8. Show how the agent’s recommendation changes when future demand is considered.
9. Ask the agent to evaluate a cluster capacity constraint.
10. Review the optimization plan and chargeback calculations.
11. Approve a small batch of simulated changes.
12. Apply those changes through the simulated ACS API.
13. Verify the resulting configuration and workload behavior.
14. Trigger a controlled failure scenario and demonstrate rollback.
15. Compare before-and-after resource allocation, chargeback estimates, and SLO outcomes.

The demo must work repeatedly without depending on unpredictable external services.

16. Technical Quality Requirements

* Keep modules small and maintainable.
* Use type annotations and clear interfaces.
* Validate inputs and outputs.
* Handle AI-provider errors and tool-call failures.
* Include unit tests for cost calculations, resource optimization, policy enforcement, placement feasibility, and rollback.
* Include integration tests for the agent-to-tool-to-ACS workflow.
* Include tests proving that policy violations cannot be executed.
* Include deterministic test fixtures and seeded synthetic data.
* Add structured logging.
* Document architecture, assumptions, limitations, and setup.
* Provide a single documented command to start the local application where practical.
* Include a reset mechanism to restore the initial simulation state.
* Avoid unnecessary infrastructure or heavyweight dependencies.

17. AI-First Development with Cursor

Use Cursor Agent mode extensively throughout the build.

Cursor should help:

* Design the architecture.
* Generate the project scaffold.
* Implement the synthetic data generator.
* Implement the metrics and forecasting tools.
* Build the AI tool-calling workflow.
* Implement the optimization and cost engines.
* Implement the simulated ACS API.
* Build the safety policies.
* Build the dashboard.
* Generate and run tests.
* Debug failures.
* Review security and correctness.
* Generate the README and demo instructions.

Use a test-driven, incremental workflow. Cursor must run the relevant tests after each major component and correct failures before proceeding.

Do not generate the entire application in one unreviewed code-generation step.

Keep a record of AI-assisted development activities, such as features generated, tests created, defects found, and fixes made, so the hackathon presentation can also demonstrate how Cursor accelerated the development process.

18. Implementation Milestones

Build in the following order:

Milestone 1: Inspect the environment, propose the architecture, define interfaces, and scaffold the repository.

Milestone 2: Generate 3,000 synthetic applications, historical metrics, cluster capacity data, and repeatable scenarios.

Milestone 3: Implement the chargeback calculator, utilization analysis, forecasting, and deterministic optimization engine.

Milestone 4: Implement the simulated ACS API, safety policies, approval workflow, audit log, and rollback.

Milestone 5: Integrate the AI model with real tool calling so it can analyze the inventory, request evidence, compare options, and produce structured recommendations.

Milestone 6: Build the dashboard and scenario controls.

Milestone 7: Implement end-to-end tests, failure scenarios, and verification.

Milestone 8: Rehearse the executive demo, document the limitations, and produce a summary of results.

At the end of each milestone, summarize what was implemented, what tests passed, what remains incomplete, and the exact commands needed to run it.

19. Definition of Done

The POC is complete when:

* It loads 3,000 synthetic applications.
* The AI agent performs genuine tool calls against the simulated platform.
* The agent identifies optimization opportunities based on evidence.
* It does not recommend unsafe reductions solely because average utilization is low.
* Cost calculations use the supplied chargeback rates.
* The optimization engine compares feasible configurations.
* Cluster placement feasibility is checked deterministically.
* All proposed changes pass through an independent policy engine.
* Changes require explicit approval and are applied only to simulated state.
* Post-change verification can succeed or trigger rollback.
* The dashboard updates from actual simulation state.
* The entire demonstration can be run locally and repeated.
* Automated tests verify the core calculations and safety requirements.
* The README documents setup, architecture, assumptions, limitations, and the demo sequence.

20. First Task for Cursor

Start with Milestone 1 only.

Inspect the available project directory and environment. Recommend a lightweight architecture suitable for macOS and explain the trade-offs.

Propose the language, framework, AI-provider integration, tool-calling approach, synthetic data model, dashboard approach, and test framework.

Then create the initial repository scaffold, architecture document, interfaces, and implementation checklist.

Do not attempt to implement all milestones at once. After the scaffold is ready, report the exact commands to run the initial project and wait for review before proceeding to Milestone 2.