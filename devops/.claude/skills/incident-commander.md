---
name: "incident-commander"
description: Incident response framework for managing technology incidents from detection through resolution and post-incident review. Covers severity classification, timeline reconstruction, PIR generation, stakeholder communication, and runbook creation. Use when responding to outages, classifying incidents, running post-mortems, or building runbooks.
---

# Incident Commander

Comprehensive incident response framework implementing battle-tested SRE practices for severity classification, timeline reconstruction, and post-incident analysis.

## Severity Classification System

### SEV1 - Critical Outage
**Definition:** Complete service failure affecting all users or critical business functions

- Customer-facing services completely unavailable
- Data loss or corruption affecting users
- Security breaches with customer data exposure
- Revenue-generating systems down

**Response:** IC assigned within 5 min, executive notification within 15 min, war room established
**Communication:** Every 15 minutes until resolution

### SEV2 - Major Impact
**Definition:** Significant degradation affecting subset of users

- Partial service degradation (>25% of users affected)
- Performance issues causing user frustration
- Non-critical features unavailable

**Response:** IC assigned within 30 min, status page update within 30 min
**Communication:** Every 30 minutes during active response

### SEV3 - Minor Impact
**Definition:** Limited impact with workarounds available

- Single feature or component affected, <25% of users impacted
- Workarounds available

**Response:** Within 2 hours during business hours
**Communication:** At key milestones only

### SEV4 - Low Impact
**Definition:** Minimal impact, cosmetic issues, or planned maintenance

**Response:** Within 1-2 business days, standard ticket tracking

## Incident Commander Role

**Responsibilities:** Command and control, communication hub, process management, post-incident leadership.

**Decision-Making:** For SEV1/2, IC has full authority — bias toward action, document decisions for review, consult SMEs but don't get blocked.

## Communication Templates

### Initial Incident Notification (SEV1/2)

```
Subject: [SEV{severity}] {Service Name} - {Brief Description}

Incident Details:
- Start Time: {timestamp}
- Severity: SEV{level}
- Impact: {user impact description}
- Current Status: {investigating/mitigating/resolved}

Technical Details:
- Affected Services: {service list}
- Symptoms: {what users are experiencing}
- Initial Assessment: {suspected root cause if known}

Response Team:
- Incident Commander: {name}
- Technical Lead: {name}
- SMEs Engaged: {list}

Next Update: {timestamp}
Status Page: {link}
War Room: {bridge/chat link}
```

### Executive Summary (SEV1)

```
Subject: URGENT - Customer-Impacting Outage - {Service Name}

Executive Summary:
{2-3 sentence description of customer impact and business implications}

Key Metrics:
- Time to Detection: {X minutes}
- Time to Engagement: {X minutes}
- Estimated Customer Impact: {number/percentage}
- Current Status: {status}
- ETA to Resolution: {time or "investigating"}

Leadership Actions Required:
- [ ] Customer communication approval
- [ ] PR/Communications coordination
- [ ] Resource allocation decisions

Incident Commander: {name} ({contact})
Next Update: {time}
```

### Customer Communication

```
We are currently experiencing {brief description} affecting {scope of impact}.

Our engineering team was alerted at {time} and is actively working to resolve the issue.
We will provide updates every {frequency} until resolved.

What we know:
- {factual statement of impact}
- {factual statement of scope}

What we're doing:
- {primary response action}
- {secondary response action}

Workaround (if available): {steps or "No workaround currently available"}

Next update: {time}
Status page: {link}
```

## Stakeholder Communication Cadence

| Stakeholder | SEV1 | SEV2 | SEV3 | SEV4 |
|-------------|------|------|------|------|
| Engineering Leadership | Real-time | 30min | 4hrs | Daily |
| Executive Team | 15min | 1hr | EOD | Weekly |
| Customer Support | Real-time | 30min | 2hrs | As needed |
| Customers | 15min | 1hr | Optional | None |
| Partners | 30min | 2hrs | Optional | None |

## Incident Response Workflow

When an incident signal is detected (failed smoke test, alert, crash loop):

1. Launch an **`incident-assessor` agent** with the full signal context: error messages, metrics, affected services, recently deployed version, and any baseline data. The agent returns:
   - Severity classification (SEV1–4)
   - Root cause assessment with confidence level
   - ROLLBACK or FIX-FORWARD recommendation with explicit reasoning
   - The exact next command or API call to execute

2. Execute the recommended action immediately. Do not wait for additional confirmation on SEV1/2.

3. After resolution, launch a second **`incident-assessor` agent** focused on PIR preparation — provide the incident timeline and all signals observed. Use its output to structure the post-incident review.

---

## Core Tools

### Usage Examples

```bash
# Classify the incident
echo '{"description": "Users reporting 500 errors, database connections timing out", "affected_users": "80%", "business_impact": "high"}' | python scripts/incident_classifier.py

# Reconstruct timeline from logs
python scripts/timeline_reconstructor.py --input assets/db_incident_events.json --output timeline.md

# Generate PIR after resolution
python scripts/pir_generator.py --incident assets/db_incident_data.json --timeline timeline.md --output pir.md

# Quick classification from text
echo "API rate limits causing customer API calls to fail" | python scripts/incident_classifier.py --format text

# PIR with specific RCA method
python scripts/pir_generator.py --incident assets/incident.json --rca-method fishbone --action-items
```

## Runbook Template

```markdown
# {Service/Component} Incident Response Runbook

## Quick Reference
- **Severity Indicators:** {list of conditions for each severity level}
- **Key Contacts:** {on-call rotations and escalation paths}
- **Critical Commands:** {list of emergency commands}

## Initial Response (0-15 minutes)
1. **Assess Severity**
   - [ ] Check {primary metric}
   - [ ] Verify {secondary indicator}
   - [ ] Classify as SEV{level} based on {criteria}

2. **Establish Command**
   - [ ] Page Incident Commander if SEV1/2
   - [ ] Create incident tracking ticket
   - [ ] Join war room: {link/bridge info}

3. **Initial Investigation**
   - [ ] Check recent deployments: {deployment log location}
   - [ ] Review error logs: {log location and queries}
   - [ ] Verify dependencies: {dependency check commands}

## Mitigation Strategies
### Strategy 1: {Name}
**Use when:** {conditions}
**Steps:**
1. {detailed step with commands}
2. {detailed step with expected outcomes}
3. {validation step}

**Rollback Plan:**
1. {rollback step}
2. {verification step}

## Recovery and Validation
1. **Service Restoration**
   - [ ] {restoration step}
   - [ ] Wait for {metric} to return to normal
   - [ ] Validate end-to-end functionality

2. **Communication**
   - [ ] Update status page
   - [ ] Notify stakeholders
   - [ ] Schedule PIR
```

## Best Practices

### During Response
- Stay composed, make decisive calls with incomplete information
- Document all actions taken and decision rationale
- Prefer rollbacks to risky fixes under pressure
- Validate fixes before declaring resolution

### Post-Incident
- Blameless culture — focus on system failures, not individuals
- Assign specific owners and due dates for action items
- Share PIRs broadly, update runbooks from lessons learned
- Look for patterns across multiple incidents

## Integration Points

- **Monitoring/Alerting:** PagerDuty/Opsgenie, Datadog/Grafana, ELK/Splunk
- **Communication:** Slack/Teams war room, Zoom/Meet video bridges, Statuspage.io
- **Documentation:** Confluence/Notion for PIR storage, GitHub for runbook version control
- **Tracking:** JIRA/Linear for action items, CI/CD pipeline integration
