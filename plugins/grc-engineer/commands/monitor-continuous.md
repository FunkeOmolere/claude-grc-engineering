---
description: Set up continuous compliance monitoring and alerting
---

# Monitor Continuous

Establishes continuous compliance monitoring with automated testing, trend analysis, and alerting for control degradation.

## Usage

```bash
/grc-engineer:monitor-continuous <frameworks> [schedule] [options]
```

## Arguments

- `$1` - Comma-separated frameworks (e.g., "SOC2,PCI-DSS,NIST")
- `$2` - Schedule (optional): "daily", "weekly", "hourly" (default: "daily")
- `$3` - Options (optional): `--slack-webhook=URL`, `--email=address`, `--dashboard`

## Examples

```bash
# Daily monitoring for multiple frameworks
/grc-engineer:monitor-continuous SOC2,PCI-DSS,NIST daily

# Hourly monitoring with Slack alerts
/grc-engineer:monitor-continuous PCI-DSS hourly --slack-webhook=https://hooks.slack.com/...

# Weekly monitoring with dashboard
/grc-engineer:monitor-continuous SOC2,NIST,ISO weekly --dashboard

# Monitor all controls continuously
/grc-engineer:monitor-continuous ALL daily --email=security@company.com
```

## Features

### 1. Automated Control Testing

- Runs `/grc-engineer:test-control` on schedule
- Tests all controls for selected frameworks
- Tracks pass/fail rates over time
- Identifies degrading controls

### 2. Trend Analysis

- 30-day compliance trend visualization
- Control effectiveness over time
- Framework compliance scoring
- Degradation detection

### 3. Alerting

- Immediate alerts on control failures
- Daily/weekly summary reports
- Slack, email, or PagerDuty integration
- Configurable thresholds

### 4. Compliance Dashboard

- Real-time compliance status
- Control health visualization
- Framework comparison
- Evidence collection status

## Output

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CONTINUOUS COMPLIANCE MONITORING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Frameworks: SOC2, PCI-DSS, NIST 800-53
Schedule: Daily @ 02:00 UTC
Status: ACTIVE

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
COMPLIANCE TREND (Last 30 Days)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Jan 1:  ████████████████████░  95% (142/150 controls)
Jan 8:  ████████████████████░  94% (141/150 controls)  ⚠ -1%
Jan 15: ████████████████████░  94% (141/150 controls)  → Stable
Jan 22: █████████████████████  96% (144/150 controls)  ✓ +2%
Jan 28: █████████████████████  96% (144/150 controls)  → Stable

Trend: ↗ IMPROVING (+1% this month)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CURRENT STATUS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Overall Compliance: 96% (144/150 controls)

By Framework:
  SOC2:       98% ████████████████████░  (49/50)
  PCI-DSS:    92% ███████████████████░░  (46/50)
  NIST 800-53: 98% ████████████████████░  (49/50)

By Status:
  ✓ Effective: 144 controls (96%)
  ⚠ Partially Effective: 4 controls (3%)
  ✗ Ineffective: 2 controls (1%)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RECENT FAILURES (Last 7 Days)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔴 HIGH PRIORITY (1):
  Jan 28: AU-11 (Audit Retention)
    Status: FAILING (2 days)
    Cause: S3 lifecycle policy misconfigured
    Impact: Logs deleted after 60 days (should be 365)
    Action: Fix S3 lifecycle policy
    Frameworks: NIST, SOC2, PCI-DSS

🟡 MEDIUM PRIORITY (3):
  Jan 25: AC-2 (Account Management)
    Status: DEGRADED (was passing, now partial)
    Cause: 3 inactive users detected >90 days
    Impact: PCI-DSS 8.1.4 violation
    Action: Disable inactive users

  Jan 22: IA-5 (Password Policy)
    Status: WARNING
    Cause: 2 users with weak passwords detected
    Impact: Best practice violation
    Action: Enforce password complexity

  ... 1 more

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CONTROL HEALTH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🟢 HEALTHY (30-day pass rate >95%):
  ✓ Access Control (AC-2): 100% (30/30)
  ✓ Encryption (SC-28): 100% (30/30)
  ✓ Logging (AU-2): 97% (29/30)
  ... 138 more

🟡 DEGRADING (pass rate declining):
  ⚠ Vulnerability Management (SI-2): 93% → 87% (-6%)
    Last 7 days: 3 failures
    Trend: Declining
    Action: Review patch management process

🔴 AT RISK (pass rate <80%):
  ✗ Audit Retention (AU-11): 73% (22/30)
    Recent failures: 8 in last 10 days
    Trend: Failing consistently
    Action: URGENT - Fix S3 lifecycle configuration

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ALERTS SENT (Last 24 Hours)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🚨 CRITICAL (1):
  22:15 - Audit retention control failed (AU-11)
  → Sent to: #security-alerts (Slack)
  → Sent to: security@company.com

⚠ WARNING (2):
  14:30 - 3 inactive users detected
  06:00 - Daily compliance summary (96%)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SCHEDULED TESTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next run: 2025-01-29 02:00 UTC (in 3h 15m)

Test schedule:
  02:00 - Full control test suite (all 150 controls)
  06:00 - Daily summary report
  14:00 - High-priority controls only
  22:00 - High-priority controls only

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CONFIGURATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Schedule: Daily @ 02:00 UTC
Alerts: Slack (#security-alerts), Email (security@company.com)
Retention: 90 days of test history
Dashboard: https://compliance.company.com/dashboard
Auto-remediation: Disabled (manual approval required)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RECOMMENDATIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Fix AU-11 S3 lifecycle policy (URGENT)
   Command: /grc-engineer:scan-iac ./terraform PCI-DSS --fix
   Estimated time: 15 minutes

2. Disable 3 inactive users
   Command: python scripts/disable_inactive_users.py --execute
   Estimated time: 10 minutes

3. Review vulnerability management process (SI-2 declining)
   Action: Schedule patch management review with ops team
   Estimated time: 2 hours
```

## Setup

### 1. Install Monitoring Agent

```bash
# Create monitoring configuration
cat > compliance-monitor.yaml <<EOF
frameworks:
  - SOC2
  - PCI-DSS
  - NIST-800-53

schedule:
  full_test: "0 2 * * *"      # Daily at 2 AM
  quick_check: "0 */6 * * *"  # Every 6 hours
  summary: "0 6 * * *"        # Daily summary at 6 AM

alerts:
  slack:
    webhook_url: "https://hooks.slack.com/services/YOUR/WEBHOOK/URL"
    channel: "#security-alerts"
  email:
    recipients:
      - security@company.com
      - compliance@company.com
    smtp_server: smtp.company.com

thresholds:
  critical: 0.80   # Alert if <80% compliance
  warning: 0.90    # Warn if <90% compliance
  degradation: -0.05  # Alert if 5% drop

dashboard:
  enabled: true
  port: 3000
  refresh_interval: 300  # 5 minutes

retention_days: 90
EOF

# Create automation metric snapshot config
cat > automation-metrics.yaml <<EOF
defaults:
  window_label: current-week
  out_dir: ./grc-data/metrics
  dimensions:
    audience: ciso-weekly
entries:
  - framework: fedramp-moderate
    provider: aws
  - framework: soc2
    controls_total: 64
    controls_automated: 22
EOF

# Deploy monitoring
/grc-engineer:monitor-continuous SOC2,PCI-DSS,NIST --config=compliance-monitor.yaml
```

### 2. CloudWatch Integration (AWS)

```bash
# Create EventBridge rule for daily tests
aws events put-rule \
  --name daily-compliance-test \
  --schedule-expression "cron(0 2 * * ? *)"

# Create Lambda function to run tests
# (Deploy test-control.js as Lambda)
```

### 3. Slack Integration

```bash
# Test Slack webhook
curl -X POST https://hooks.slack.com/services/YOUR/WEBHOOK/URL \
  -H 'Content-Type: application/json' \
  -d '{"text":"Compliance monitoring active: 96% compliant"}'
```

## Dashboard

The monitoring dashboard provides:

- **Real-time Status**: Current compliance percentage
- **30-Day Trend**: Line chart of compliance over time
- **Control Health**: Green/yellow/red status per control
- **Framework Comparison**: Side-by-side framework scores
- **Recent Failures**: Last 10 control failures with remediation
- **Alert History**: All alerts sent in last 30 days

Access dashboard:

```bash
http://localhost:3000/compliance-dashboard
```

## Alerting Rules

### Critical Alerts (Immediate)

- Overall compliance drops below 80%
- Any control fails for 2+ consecutive days
- PCI-DSS compliance drops below 90%
- Security control fails (encryption, access control)

### Warning Alerts (Daily Digest)

- Overall compliance 80-90%
- Control degrading (5% drop in 7 days)
- Non-security control failures
- Upcoming audit deadline (<30 days)

### Info Alerts (Weekly Summary)

- Compliance trend (improving/declining)
- Controls tested this week
- Evidence collection status
- Recommendations for improvement

## CI/CD Integration

```yaml
# .github/workflows/compliance-monitor.yml
name: Continuous Compliance Monitoring

on:
  schedule:
    - cron: '0 2 * * *'  # Daily at 2 AM UTC
  workflow_dispatch:     # Manual trigger

jobs:
  test-compliance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm ci

      - name: Ensure automation snapshot config exists
        run: test -f automation-metrics.yaml

      - name: Run Control Tests
        run: |
          /grc-engineer:monitor-continuous SOC2,PCI-DSS,NIST daily \
            --output=json > results.json

      - name: Check Compliance Threshold
        run: |
          COMPLIANCE=$(jq '.summary.compliance_percentage' results.json)
          if (( $(echo "$COMPLIANCE < 0.90" | bc -l) )); then
            echo "::error::Compliance below 90%: $COMPLIANCE"
            exit 1
          fi

      - name: Record automation snapshots
        run: |
          node plugins/grc-engineer/scripts/record-automation-metrics.js \
            --config=automation-metrics.yaml \
            --window-label=current-week

      - name: Send to Slack
        if: failure()
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {
              "text": "⚠️ Compliance Alert: Dropped to $COMPLIANCE%",
              "channel": "#security-alerts"
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

## Metrics Collected

- **Compliance Score**: Percentage of passing controls
- **Framework Scores**: Individual framework compliance
- **Control Health**: Pass rate per control over 30 days
- **Test Execution Time**: Duration of test runs
- **Failure Rate**: Controls failing per day
- **Remediation Time**: Time to fix failed controls
- **Evidence Coverage**: Percentage of controls with evidence

## Related Commands

- `/grc-engineer:test-control` - Test individual controls
- `/grc-engineer:scan-iac` - Scan infrastructure for violations
- `/grc-engineer:collect-evidence` - Collect compliance evidence
- `/grc-engineer:record-automation-metrics` - Persist weekly automation coverage history
- `/grc-engineer:generate-implementation` - Implement controls
