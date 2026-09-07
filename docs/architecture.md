# RealEstate AI OS — Technical Architecture

## Overview

RealEstate AI OS is a modular n8n automation architecture for managing the real-estate lead lifecycle.

The system is organized into independent modules that handle intake, qualification, CRM operations, routing, booking, recovery, follow-up, content generation, analytics, and error handling.

The architecture is designed so that incoming lead data is normalized early and then passed through deterministic workflow logic and AI-powered decision layers.

---

## High-Level Flow

```text
Lead Sources
     ↓
Input Normalization
     ↓
Validation
     ↓
AI Qualification
     ↓
Spam Detection
     ↓
Duplicate Detection
     ↓
Agent Assignment
     ↓
CRM Upsert
     ↓
Lead Routing
     ↓
Hot / Warm / Cold
     ↓
Notifications / Follow-Up / Nurture
     ↓
Booking
     ↓
Analytics

Module 1 — Multi-Channel Intake

The intake layer receives leads from multiple entry points and converts them into a consistent internal structure.

Inputs
Website chat
Contact forms
WhatsApp
Messenger
Voice / call workflows
Responsibilities
Receive incoming data
Normalize fields
Validate required information
Produce a consistent lead object
Pass validated data downstream

The purpose of normalization is to prevent downstream modules from needing separate logic for every lead source.
Module 2 — AI Qualification

Validated lead data is passed into an AI qualification layer.

The AI evaluates the lead and produces structured information that can be used by the automation engine.

Qualification Data Includes
Intent
Property type
Suburb
Budget
Bedrooms
Bathrooms
Timeline
Finance status
Sentiment
Urgency
Spam probability
Confidence
Lead score
Lead tier
Next action
Reasoning
AI response

The AI output is expected to follow a strict JSON structure.

This allows workflow nodes to make decisions using structured fields rather than parsing unrestricted natural-language AI responses.

Module 3 — Duplicate Detection & CRM

After qualification, the system checks whether the lead already exists.

The duplicate detection process uses multiple lead fields rather than depending entirely on a single identifier.

CRM Responsibilities
Search existing leads
Detect potential duplicates
Assign an agent
Create new records
Update existing records
Preserve qualification information
Maintain a consistent lead schema

Module 4 — Lead Routing & Notifications

The qualification result determines the lead's next path.
              Lead
                ↓
        Qualification
                ↓
        ┌───────┼───────┐
        ↓       ↓       ↓
       HOT     WARM    COLD
        ↓       ↓       ↓
     Alerts   Follow-  Nurture
              Up
Hot Leads

Hot leads receive priority treatment.

Possible actions include:

Agent notification
Email notification
Slack notification
Booking workflow
Warm Leads

Warm leads can enter automated follow-up sequences.

Cold Leads

Cold leads can remain in the CRM for longer-term nurturing.

Module 5 — Appointment Booking

The booking module connects qualified leads with appointment scheduling.

Flow
Qualified Lead
      ↓
Find Available Slots
      ↓
Offer Next 3 Options
      ↓
Customer Selects Slot
      ↓
Validate Selection
      ↓
Create Calendar Event
      ↓
Confirmation
The booking architecture also supports:

Rescheduling
Cancellation

This allows the appointment lifecycle to be handled through automation rather than requiring every action to be manually coordinated.

Module 6 — Missed Call Recovery

Missed calls are treated as another entry point into the lead-management process.

When a call cannot be answered, the recovery workflow can:

Capture the missed-call event
Identify the caller
Send a response
Store/update the lead
Continue the lead journey

This creates an automated recovery path for potentially high-intent prospects.

Module 7 — Follow-Up Sequence Engine

The follow-up engine periodically scans CRM data and determines which leads require another touchpoint.

Sequence
Day 1
  ↓
Day 3
  ↓
Day 7
  ↓
Day 14
The sequence does not blindly continue.

Before progressing, the system checks whether the lead has:

Replied
Booked an appointment
Opted out

If a stopping condition is detected, unnecessary follow-ups are prevented.

Module 8 — AI Listing Writer

The system also contains a property-content generation module.

Flow
Property Information
        ↓
AI Content Generation
        ↓
Structured Output
        ↓
13 Content Variants
The workflow generates multiple content variants from property information, reducing repetitive content-production work.

Module 9 — Analytics

Operational events are logged so activity across the automation can be analyzed.

Responsibilities
Record events
Track workflow activity
Process scheduled analytics
Generate summary rollups

This provides visibility into system activity rather than treating every workflow execution as an isolated event.

Module 10 — Global Error Handling

The architecture includes a centralized error-handling path.

The objective is to capture failures from different parts of the automation and provide a consistent mechanism for handling them.

Reliability Mechanisms
Input validation
Structured AI output
Score validation
Spam detection
Duplicate detection
Conditional routing
Follow-up stop conditions
CRM upsert logic
Error handling

AI Decision Architecture

AI is used as a decision layer inside a deterministic automation system.
Raw Data
   ↓
Validation
   ↓
AI Analysis
   ↓
Structured JSON
   ↓
Validation
   ↓
Business Rules
   ↓
Automation Action
This separation is important.

The AI does not directly control every downstream action.

Instead, AI produces structured information and workflow logic determines what should happen next.

Data Flow

A typical lead can move through the system as follows:
Customer Inquiry
       ↓
Webhook
       ↓
Normalized Lead Object
       ↓
Validated Data
       ↓
AI Qualification
       ↓
Score + Classification
       ↓
Spam Check
       ↓
Duplicate Detection
       ↓
Agent Assignment
       ↓
CRM Upsert
       ↓
Routing
       ↓
Notification / Follow-Up
       ↓
Booking
       ↓
Analytics
Design Principles

The architecture follows several principles.

1. Normalize Early

Different input channels should produce a consistent internal lead structure.

2. Validate Before Acting

Invalid or incomplete data should not blindly trigger downstream actions.

3. Keep AI Structured

AI responses should be converted into predictable structured data.

4. Separate AI From Business Logic

AI provides analysis while deterministic workflow logic controls execution.

5. Stop Automation When Conditions Change

Follow-up sequences should stop when a lead replies, books, or opts out.

6. Handle Failures

The system should account for failed executions and unexpected conditions rather than assuming every workflow succeeds.

Technology Layer
                 ┌────────────────────┐
                 │       n8n          │
                 │ Workflow Engine    │
                 └─────────┬──────────┘
                           │
          ┌────────────────┼────────────────┐
          ↓                ↓                ↓
        AI / LLMs       REST APIs        Webhooks
          │                │                │
          └────────────────┼────────────────┘
                           ↓
                  Business Logic
                           ↓
             CRM / Google Services
                           ↓
                  Notifications
Security

Public workflow files should contain no production credentials or private API keys.

Credentials should be configured separately inside n8n or through the appropriate credential management system.

❌ API Keys
❌ Access Tokens
❌ Passwords
❌ Production Credentials

✅ Credential References
✅ Example Configuration
✅ Sanitized Workflow Logic
Project Scope

This repository contains the architecture and implementation of an independent portfolio project.

Deployment-specific credentials and private account configuration are intentionally excluded.

The system can be adapted to different:

CRM platforms
AI providers
Communication channels
Calendar systems
Business requirements
Project Status

Independent Project / Portfolio System

This project was built as a self-initiated automation system to demonstrate practical AI automation architecture, workflow engineering, and business process automation.

It is not presented as commissioned client work.

The repository focuses on the system architecture, workflow logic, documentation, and implementation approach.

This keeps the CRM synchronized with the automation.
