# AI-Assisted Proposal Triage Design

**Author:** Affan Zahid  
**Assessment reference:** MATACS-TH-2026-02  
**Time spent:** Approximately 60 minutes

This repository describes a reliable, human-supervised process for triaging contractor proposals. It separates AI-assisted evidence extraction from deterministic eligibility checks and scoring so that rankings remain traceable and repeatable.

## Deliverables

- [Process-flow diagram](diagram/proposal-triage-flow.png)
- [Design write-up](WRITEUP.md)
- [Extraction prompt](PROMPTS.md)
- [Expected sample ranking](ranking.json)

## AI tool use

I used an AI assistant to challenge the process design, tighten the wording, and help produce the diagram and repository files. I rejected the suggestion that the model should score proposals directly: scoring belongs in deterministic code using the admin's versioned criteria, while AI is limited to extracting cited evidence. I also corrected the sample treatment so the instruction embedded in Proposal C is flagged as untrusted proposal content rather than followed.

## Design summary

The system stores category criteria as versioned configuration. Uploaded files are scanned and text-extracted, an AI model maps proposal evidence into a strict schema, and a reviewer confirms uncertain or suspicious fields. Plain code then applies mandatory gates, weighted scoring rules, deterministic tie-breakers, and generates an evidence-backed ranking. The final award always remains a human decision.
