# RealEstate AI OS — Setup Guide

This guide explains how to import, configure, and deploy the RealEstate AI OS workflow in n8n.

> **Important:** The workflow file included in this repository is sanitized. Credentials and secrets have been removed and must be configured in the user's own n8n instance.

---

## 1. Requirements

### Required

- n8n
- Google Gemini API access
- Google Sheets
- Gmail
- Google Calendar

### Optional / Channel-Specific

- Slack
- Twilio
- WhatsApp
- Messenger
- Website chat or contact-form webhook source

The exact integrations required depend on which modules and lead channels are enabled.

---

# 2. Import the Workflow

Download or clone this repository and locate:
In n8n:

Open your n8n workspace.
Select Workflows.
Choose Import from File.
Select:
realestate-ai-os-sanitized.json
Open the imported workflow.
Review the nodes and connections before activating it.

The workflow is provided as a sanitized architecture reference and deployment starting point.

3. Configure Credentials

The sanitized workflow does not contain live credentials.

Create the required credentials inside n8n and connect them to the appropriate nodes.

Google Gemini

Used for AI-powered lead qualification and structured lead analysis.

Configure:

Gemini API credential
API access
Model configuration used by the workflow

Do not place API keys directly inside workflow JSON.

Google Sheets

Used for CRM-style lead storage, tracking, and workflow state.

Configure:

Google account authentication
Access to the required spreadsheet
Appropriate sheet/tab permissions

Replace any example spreadsheet references with your own resources.

Gmail

Used for automated communication and internal notifications.

Configure:

Gmail OAuth authentication
Sending account
Required permissions

Use a dedicated business email account where appropriate.

Google Calendar

Used by the booking module.

Configure:

Google Calendar authentication
Target calendar
Calendar permissions

The booking workflow can then create events after the lead confirms an available appointment slot.

Slack

Slack is used for internal notifications, particularly for important lead events such as hot-lead alerts.

Configure:

Slack authentication
Target workspace
Notification channel

This integration can be disabled if Slack notifications are not required.

Twilio

Twilio can be used for communication and missed-call recovery functionality.

Configure:

Twilio authentication
Appropriate messaging/voice resources
Required phone numbers
Webhook endpoints

Only enable the relevant Twilio modules when the required communication infrastructure is available.

4. Configure Webhooks

The system supports multiple lead-entry channels.

Examples include:

Website Chat
Contact Form
WhatsApp
Messenger
Voice Receptionist

Each channel should send its data to the appropriate n8n webhook.

The workflow then normalizes incoming information into a common lead structure.

Example:

{
  "name": "Sarah Mitchell",
  "email": "sarah@example.com",
  "phone": "+61000000000",
  "source": "website_chat",
  "message": "I'm looking to buy a 3 bedroom house in Parramatta.",
  "property_type": "house",
  "suburb": "Parramatta",
  "budget": "AUD 900,000",
  "timeline": "within 2 months"
}
5. Configure CRM Storage

The workflow requires a destination for lead and workflow data.

Google Sheets can be used as the CRM-style storage layer.

Recommended fields include:

lead_id
name
email
phone
source
intent
property_type
suburb
budget
bedrooms
bathrooms
timeline
finance_approved
sentiment
urgency
spam_probability
confidence
lead_score
lead_tier
agent
status
last_contact
next_followup
booking_status
created_at
updated_at

The exact fields should match the workflow implementation being deployed.

6. Configure Lead Routing

The AI qualification stage assigns a lead score and tier.

Example tiers:

HOT
WARM
COLD

These tiers determine downstream actions.

HOT

High-priority leads can trigger:

Agent notification
CRM update
Immediate follow-up
Appointment/inspection flow
WARM

Warm leads can enter:

Follow-up sequences
Additional qualification
Agent review
COLD

Cold leads can remain in the CRM for:

Future follow-up
Nurturing
Requalification
7. Configure Appointment Booking

The booking module requires Google Calendar access.

The general flow is:

Lead requests appointment
        ↓
Available slots generated
        ↓
Next available slots offered
        ↓
Customer confirms
        ↓
Calendar event created
        ↓
CRM updated

The deployment should define:

Calendar
Appointment duration
Availability rules
Time zone
Event title
Event description
Customer information
8. Configure Follow-Up Sequences

The follow-up engine can scan CRM records and determine whether a lead requires another follow-up.

The documented sequence uses stages such as:

Day 1
Day 3
Day 7
Day 14

Follow-up should stop when the lead:

Replies
Books an appointment
Opts out
Reaches another configured stopping condition
9. Configure AI Listing Writer

The listing writer module generates multiple content variations for a property.

The documented system generates:

13 content variants

These can be used for different listing and marketing contexts.

Before production use, review generated content and ensure property information is accurate.

10. Configure Analytics

The analytics layer records workflow events and can generate scheduled summaries.

Useful events include:

lead_received
lead_qualified
lead_rejected
lead_routed
agent_notified
followup_sent
appointment_requested
appointment_booked
appointment_cancelled
lead_replied
lead_opted_out
workflow_error

The exact event schema can be adapted to the deployment environment.

11. Error Handling

The system includes a global error-handling architecture.

Production deployments should monitor:

Failed API requests
Invalid webhook payloads
AI parsing failures
Missing CRM records
Calendar failures
Email failures
Messaging failures
Authentication failures

External API calls should also use appropriate retry and fallback strategies where required.

12. Security

Never commit:

API keys
OAuth tokens
Passwords
Webhook secrets
Private customer information
Production CRM data
Real email addresses belonging to customers

Use n8n's credential system for authentication.

Use fictional data in examples and documentation.

The repository intentionally contains a sanitized workflow instead of production credentials.

13. Deployment Checklist

Before activating the workflow:

 Import the sanitized workflow
 Configure Gemini
 Configure Google Sheets
 Configure Gmail
 Configure Google Calendar
 Configure Slack if required
 Configure Twilio if required
 Replace example resources
 Configure webhook URLs
 Verify CRM fields
 Verify calendar settings
 Verify notification destinations
 Review AI output handling
 Review error handling
 Test with fictional/sample data
 Confirm credentials are not exposed
 Activate production workflows only after deployment testing
14. Repository Examples

Sample input and output are available in:

examples/
├── sample-input.json
└── sample-output.json

These demonstrate the expected transformation from an incoming real-estate lead to a structured, qualified lead record.

15. Project Classification

This is an independent automation engineering project built to demonstrate production-oriented workflow architecture.

It is not presented as client work.

The project demonstrates practical experience with:

n8n workflow development
AI/LLM integration
Webhooks
API integrations
CRM automation
Lead qualification
Routing logic
Appointment automation
Follow-up systems
Notifications
Analytics
Error handling
Workflow security

```text
workflows/
└── realestate-ai-os-sanitized.json
