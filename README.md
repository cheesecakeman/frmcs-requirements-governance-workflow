# frmcs-requirements-governance-workflow
README – n8n Workflow Deliverable

Title
Three-Agent n8n Prototype for Requirements Governance Across Levels

Author
Kristi Kapllani

Thesis Context
This deliverable accompanies the master thesis
“Workflow-Level Automation for Requirements Governance Across Levels: Evidence from GSM-R to FRMCS Migration”.

It contains the bounded n8n prototype used to demonstrate and evaluate a staged workflow for requirements governance across heterogeneous sources and levels of abstraction in the GSM-R to FRMCS migration context.

Deliverable Structure
This deliverable is organised into three main parts:

1. workflow_files/
   - Agent1_V1.json
   - Agent 2_V1.json
   - Agent3_V1.json

2. synthetic_data/
   - input/
     - requirements input
     - reference document
   - output/
     - Agent 1 CSV output
     - Agent 2 CSV output
     - Agent 3 CSV output
     - Agent 3 design brief

3. real_data/
   - input/
     - requirements input
     - reference document
   - output/
     - Agent 1 CSV output
     - Agent 2 CSV output
     - Agent 3 CSV output
     - Agent 3 design brief

Purpose
The prototype was designed as a staged, human-controlled workflow for transforming requirement-related inputs into structured, source-grounded, comparison-annotated, and actionable governance outputs.

The workflow is bounded and research-oriented. It was developed for thesis demonstration and evaluation, not as a production-ready industrial system.

Workflow Overview
The prototype consists of three separate n8n workflows, each representing one agent in the staged architecture.

Agent 1 – Intake and Grounding
Purpose:
Agent 1 reads the source requirements document, extracts requirement-relevant statements, preserves source grounding, and converts them into a structured CSV output.

Main logic:
- manually triggered
- downloads the source PDF from Google Drive
- extracts PDF text
- splits the text into overlapping character-based chunks
- uses GPT-4o to extract requirement-relevant statements
- rewrites requirement text into SHALL format
- preserves source and traceability fields
- exports the structured result as CSV

Main output fields:
- id
- text
- sourcePassage
- sourceDoc
- sourceSection
- sourceRef
- stakeholderType
- abstractionLevel
- ambiguityFlag

Agent 2 – Comparison and Clarification
Purpose:
Agent 2 reads the structured output from Agent 1 together with a reference specification and adds comparison- and clarification-related fields.

Main logic:
- manually triggered
- downloads the Agent 1 CSV output from Google Drive
- downloads the reference PDF from Google Drive
- extracts and merges the requirement data and specification text
- filters and selects requirements for comparison
- uses GPT-4o to compare requirements against the reference specification
- generates structured comparison results
- exports the result as CSV

Main added fields:
- clarifiedText
- comparisonNote
- mismatchFlag
- stricterSource
- openQuestion
- reviewStatus

Agent 3 – Actionable Output
Purpose:
Agent 3 reads the comparison-annotated output from Agent 2 and converts it into governance-oriented outputs for engineering and management audiences.

Main logic:
- manually triggered
- downloads the Agent 2 CSV output from Google Drive
- cleans and aggregates the structured requirement records
- uses GPT-4o to assign governance-related fields
- generates a structured requirement table
- in parallel, generates a plain-language design brief
- uploads both outputs to Google Drive

Main added fields:
- owner
- evidenceType
- ownerConfidence
- priority
- actionNote

Additional output:
- design brief in plain text format

Human-in-the-Loop Logic
The workflow is not fully autonomous.
It is intended to be used with human review between stages.

The intended checkpoint logic is:
- after Agent 1: review intake, extraction, and source grounding
- after Agent 2: review comparison results, mismatches, and open questions
- after Agent 3: review ownership, priority, and actionability of outputs

This means the prototype should be understood as a decision-support workflow rather than an autonomous requirements-management system.

Expected Inputs
For each dataset folder, the input subfolder contains:
- the source requirements input document
- the reference document used for comparison

The synthetic dataset is included to demonstrate end-to-end workflow behaviour on a controlled example.
The real dataset is included to demonstrate bounded use on official FRMCS specification material.

Expected Outputs
For each dataset folder, the output subfolder contains:
- Agent 1 CSV output
- Agent 2 CSV output
- Agent 3 CSV output
- Agent 3 design brief

These outputs represent the staged enrichment of the requirement record across the workflow.

How to Use the Workflow Files
1. Import each JSON workflow into n8n.
2. Reconnect the required credentials.
3. Update file references and Google Drive folder references if your environment differs from the original setup.
4. Run Agent 1 first.
5. Review the Agent 1 output.
6. Run Agent 2 using the Agent 1 output and the relevant reference document.
7. Review the Agent 2 output.
8. Run Agent 3 using the Agent 2 output.
9. Review the final CSV output and design brief.

Required Services and Credentials
The workflows require:
- Google Drive OAuth2 credentials
- OpenAI API credentials

The current workflow exports are configured to use Google Drive and GPT-4o. Agent 1, Agent 2, and Agent 3 are all manually triggered and depend on file retrieval and output storage through Google Drive.

Implementation Notes
Agent 1 uses character-based chunking with overlap and then performs a second normalisation pass to rewrite extracted requirement text into SHALL format while preserving the other fields.

Agent 2 compares requirements against a downloaded reference PDF and, in the current exported version, filters for requirements with explicit source references containing “10.11” and processes a bounded subset of up to ten requirements for comparison.

Agent 3 produces two parallel outputs from the Agent 2 result: a structured requirement table and a plain-language design brief.

Limitations
- This is a bounded research prototype, not a production system.
- Human review remains necessary between stages.
- Output quality depends on the correctness of upstream outputs and the suitability of the selected reference document.
- The current Agent 2 implementation is bounded in scale and does not process unlimited requirement sets in one comparison run.
- The workflows contain environment-specific references such as Google Drive file IDs, folder IDs, and credential bindings.

Important Sanitisation Note
Before external submission or sharing, remove or replace:
- credential names and IDs
- Google Drive file IDs
- Google Drive folder IDs
- any environment-specific account details

Relation to the Thesis
These workflows operationalise the staged prototype described in the thesis:
- Agent 1 corresponds to intake and grounding
- Agent 2 corresponds to comparison and clarification
- Agent 3 corresponds to actionable output generation

Together, they implement the bounded workflow used in the synthetic and real-document demonstrations and in the evaluation presented in the thesis.

Important for Reuse
Anyone attempting to reimplement or rerun the workflow should verify the expected input and output structure between agents before execution. In particular, they should check:
- the filenames and file locations used by each workflow
- the field names expected in the CSV handoff between Agent 1, Agent 2, and Agent 3
- the naming of output artefacts generated at each stage
- the Google Drive references and destination folders configured in the exported workflows

The workflows are stage-dependent: Agent 2 expects the structured CSV produced by Agent 1, and Agent 3 expects the comparison-annotated CSV produced by Agent 2. If field names, file names, or storage paths are changed, the downstream stages may need to be adjusted accordingly.

