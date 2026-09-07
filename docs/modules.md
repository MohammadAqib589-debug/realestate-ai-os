# RealEstate AI OS — Module Implementation

This document describes the implementation responsibilities of the major modules inside the RealEstate AI OS n8n workflow.

The system is implemented as a modular workflow architecture where each stage has a defined responsibility and passes structured data to the next stage.

---

# Module 1 — Multi-Channel Intake

## Purpose

Accept lead information from multiple channels and normalize it into a common structure.

## Entry Points

The workflow includes webhook-based entry points for:

- Website chat
- Contact forms
- WhatsApp
- Messenger
- Voice / call workflows

## Processing

Incoming payloads are transformed into a consistent lead representation.

The intake layer is responsible for handling differences between channel-specific payload formats so that downstream modules can work with standardized fields.

## Output

A normalized lead object containing the information required by qualification, CRM, routing, and follow-up processes.

---

# Module 2 — AI Qualification

## Purpose

Analyze incoming lead information and convert it into structured qualification data.

## AI Output

The qualification layer produces structured fields including:

- Intent
- Property type
- Suburb
- Budget
- Bedrooms
- Bathrooms
- Timeline
- Finance approval status
- Sentiment
- Urgency
- Spam probability
- Confidence
- Lead score
- Lead tier
- Next action
- Reasoning
- AI reply

## AI Constraints

The qualification request is designed to return JSON matching a predefined schema.

The workflow uses deterministic AI configuration for qualification so that the same input structure can be processed consistently.

## Validation

The workflow validates the returned qualification data before using it for downstream routing decisions.

---

## Module 3 — Spam Detection

## Purpose

Prevent obvious spam or low-quality submissions from progressing through the normal lead pipeline.

Spam probability is included in the AI qualification result and can be used as part of the decision-making process.

## Processing

```text
Lead
  ↓
AI Analysis
  ↓
Spam Probability
  ↓
Qualification / Routing Decision
This allows spam detection to happen before expensive or high-value downstream actions are triggered.

Module 4 — Duplicate Detection & CRM
Purpose

Prevent repeated submissions from unnecessarily creating multiple CRM records.

Processing

The workflow checks existing CRM data using multiple lead attributes.

Potential matches are evaluated before the system determines whether the lead should create a new record or update an existing one.

CRM Responsibilities
Search existing leads
Detect duplicates
Assign agents
Create new records
Update existing records
Preserve qualification information
Maintain lead data
Result

The CRM becomes the persistent record of the lead while the n8n workflow handles the surrounding automation.

Module 5 — Agent Assignment
Purpose

Determine which agent should receive responsibility for a lead.

Agent assignment occurs as part of the CRM processing and routing stage.

The assigned agent becomes part of the lead's structured data and can subsequently be used by notification and operational workflows.

Module 6 — Lead Routing & Notifications
Purpose

Route leads according to qualification and priority.

The primary classification is:

                Lead
                  ↓
           Lead Qualification
                  ↓
        ┌─────────┼─────────┐
        ↓         ↓         ↓
       HOT       WARM      COLD
        ↓         ↓         ↓
     Priority   Follow-Up  Nurture
      Alerts
Hot Leads

Hot leads can trigger priority notifications.

The implementation includes notification paths using:

Gmail
Slack

The notification contains important lead information so the assigned agent can act quickly.

Warm Leads

Warm leads can enter the follow-up sequence.

Cold Leads

Cold leads remain available for CRM-based nurturing and future engagement.

Module 7 — Appointment Booking
Purpose

Automate the process of turning an interested lead into a scheduled inspection or appointment.

Booking Flow
Qualified Lead
      ↓
Find Available Slots
      ↓
Offer Next 3 Slots
      ↓
Customer Selection
      ↓
Validate Selection
      ↓
Create Calendar Event
      ↓
Send Confirmation
Calendar Integration

The booking workflow uses calendar availability to identify available appointment times.

The system is configured around the target operating timezone and checks calendar events before presenting available slots.

Customer Selection

The customer can select one of the proposed options.

The selected option is validated before an event is created.

Supported Operations

The architecture also includes paths for:

New booking
Rescheduling
Cancellation
Booking confirmation
Module 8 — Missed Call Recovery
Purpose

Recover potential leads when an incoming call is missed.

Flow
Missed Call
     ↓
Identify Caller
     ↓
Send Recovery Message
     ↓
Store / Update Lead
     ↓
Continue Lead Journey

The workflow can send a recovery message to the caller and persist the event in the lead-management system.

This creates an automated response path instead of leaving the missed call as an isolated event.

Module 9 — Follow-Up Sequence Engine
Purpose

Automatically continue communication with leads who have not yet converted.

The workflow periodically evaluates CRM records to determine which follow-up stage should execute.

Sequence
Day 1
  ↓
Day 3
  ↓
Day 7
  ↓
Day 14
Stop Conditions

The sequence is designed to stop when an appropriate conversion or termination event occurs.

Examples include:

Lead replies
Appointment is booked
Lead opts out

This prevents the system from continuing to send unnecessary messages after the lead has already responded or converted.

Module 10 — AI Follow-Up Generation
Purpose

Generate context-aware follow-up messages for the current stage of the nurture sequence.

The AI receives the relevant lead information and follow-up stage and returns structured output containing:

Email subject
Email body

The generated message is then passed into the communication stage.

Module 11 — AI Listing Writer
Purpose

Generate multiple marketing-content variations from property information.

Flow
Property Information
        ↓
AI Content Generation
        ↓
Structured Output
        ↓
13 Content Variants

The module is designed to reduce repetitive property-content creation.

The generated variants can be adapted for different real-estate marketing and communication requirements.

Module 12 — Analytics & Event Logging
Purpose

Record operational activity across the automation.

Important workflow events can be stored so that system activity can later be aggregated and reviewed.

Responsibilities
Record events
Store timestamps
Track workflow activity
Process scheduled analytics
Generate summary rollups

This creates a historical layer for understanding system activity.

Module 13 — Global Error Handling
Purpose

Provide a centralized mechanism for handling workflow failures.

Instead of assuming that every node will execute successfully, the architecture includes a global error-handling path.

Reliability Layer

The system incorporates:

Input validation
AI output validation
Score validation
Spam detection
Duplicate detection
Conditional routing
Follow-up stop conditions
CRM upsert logic
Error handling

The goal is to prevent unexpected data or failed operations from silently propagating through the system.

Data Contract

The automation relies on structured lead data as it moves between modules.

A representative lead object can contain fields such as:

{
  "lead_id": "example-lead-id",
  "name": "Example Lead",
  "email": "example@example.com",
  "phone": "+61000000000",
  "intent": "buyer",
  "property_type": "house",
  "suburb": "Example Suburb",
  "budget": "AUD 800,000",
  "bedrooms": "3",
  "bathrooms": "2",
  "timeline": "1-3 months",
  "lead_score": 82,
  "lead_tier": "hot",
  "urgency": "high",
  "next_action": "offer inspection",
  "assigned_agent": "Example Agent"
}

This is an example structure only and contains no production lead information.

AI + Deterministic Logic

The architecture intentionally separates AI reasoning from deterministic workflow execution.

Incoming Data
      ↓
Validation
      ↓
AI Analysis
      ↓
Structured JSON
      ↓
Output Validation
      ↓
Business Rules
      ↓
Workflow Decision
      ↓
External Action

AI is therefore used as one component of the automation rather than being given unrestricted control over the entire system.

External Services

The workflow architecture integrates with external services for different operational responsibilities.

Service	Role
n8n	Workflow orchestration
Gemini	AI qualification and content generation
Google Sheets	Lead / event data persistence
Gmail	Email communication and notifications
Google Calendar	Appointment availability and events
Slack	Internal lead notifications
Twilio	Missed-call communication

Credentials are intentionally excluded from the public workflow export.

Workflow Design Principles
Modular Architecture

Major business functions are separated into logical modules so individual components can be modified without redesigning the entire system.

Structured AI

AI outputs are expected to follow predefined structures wherever downstream automation depends on the result.

Validation

Data is validated before important downstream actions are triggered.

Persistent State

Lead and event information is stored so that future workflow executions can reference previous activity.

Conditional Execution

Different lead states result in different workflow paths.

Stop Conditions

Follow-up workflows contain conditions that prevent unnecessary communication after a lead replies, books, or opts out.

Failure Awareness

The system includes explicit error-handling mechanisms instead of relying exclusively on successful execution paths.

Security & Deployment

The public repository contains a sanitized workflow export.

Production credentials should be configured separately inside n8n.

Never commit:

API keys
Access tokens
Passwords
OAuth secrets
Production customer data
Private account identifiers

The repository is intended to demonstrate workflow architecture and implementation rather than expose deployment credentials.
