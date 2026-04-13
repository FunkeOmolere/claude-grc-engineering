# claude-grc-engineering

I've worked both sides of FedRAMP: years as a 3PAO assessor, and I build open-source GRC tooling for the teams stuck doing the work by hand. Every team I've assessed ends up re-inventing the same pipeline: pull evidence from AWS, GitHub, GCP, and Okta; map it to SOC 2 or NIST 800-53 or FedRAMP Moderate; generate a gap report; fight with OSCAL. I wanted one toolkit that did the whole pipeline end-to-end without bolting me into a vendor platform. This is it.

Install as a [Claude Code](https://docs.claude.com/claude-code) plugin. Run:

```
/grc-engineer:gap-assessment SOC2,FedRAMP-Moderate --sources=aws,github
```

You get a prioritized, effort-estimated, remediation-linked gap report backed by 1,468 [Secure Controls Framework](https://securecontrolsframework.com) controls crosswalked to 249 frameworks.

> Not affiliated with Anthropic. Independent open-source project. Claude, Anthropic, and any related marks are property of their respective owners.

## What I'm taking a position on

A few opinionated design choices worth naming up front, since they're most of what makes this different from a Vanta or Drata clone.

**SCF is the right crosswalk source.** Everyone rolls their own control-mapping tables. They're usually incomplete, and nobody maintains them past the quarter they were built in. SCF has 1,468 controls mapped bidirectionally to 249 frameworks, publishes quarterly, and ships as a static JSON API. Use it as the backbone. Stop hand-maintaining CSVs.

**Connectors should be thin.** Most GRC platforms bundle giant agents that do everything. That's a vendor lock-in pattern, not an engineering pattern. Every connector here is a few hundred lines that shells out to tools you already have (`aws`, `gcloud`, `gh`, direct Okta API). You can rip and replace any of them without touching the rest of the toolkit.

**Framework plugins don't reproduce standard text.** ISO 27001, PCI DSS, and HITRUST CSF text is copyrighted. Plenty of GRC tools publish that text inside their product and hope nobody notices. This toolkit references control IDs and ships implementation guidance in my own words. Your licensed copy of the standard is the source of truth.

**Vanta, Drata, OneTrust, and Archer are good at what they do.** They're also expensive, slow to extend, and assume you have a compliance team. This is for teams that want the engineering layer without the platform lock-in, and for 3PAOs and assessors who want to cross-check what a platform is reporting.

## 60-second install

```bash
# In Claude Code
/plugin marketplace add ethanolivertroy/claude-grc-engineering
/plugin install grc-engineer@ethanolivertroy-plugins
```

For a first run with no cloud credentials, use your GitHub account as the data source:

```bash
/plugin install github-inspector@ethanolivertroy-plugins
/plugin install soc2@ethanolivertroy-plugins
/github-inspector:setup
/github-inspector:collect --scope=@me
/grc-engineer:gap-assessment SOC2 --sources=github-inspector
```

Full walkthrough: [`docs/QUICKSTART.md`](docs/QUICKSTART.md).

## What you can do with it

| Workflow | Command |
|---|---|
| Gap-assess an environment against one or many frameworks at once | `/grc-engineer:gap-assessment` |
| Scan Terraform, CloudFormation, or Kubernetes for compliance violations, optionally auto-fix | `/grc-engineer:scan-iac` |
| Validate a control end-to-end: config, functionality, compliance | `/grc-engineer:test-control` |
| Generate remediation (Terraform modules, Python evidence scripts, Rego/Cedar policies) | `/grc-engineer:generate-implementation`, `generate-policy` |
| See one control across every framework it maps to | `/grc-engineer:map-controls-unified` |
| Find conflicting requirements across frameworks, with "most-restrictive wins" resolution | `/grc-engineer:find-conflicts` |
| Optimize multi-framework implementation (satisfy many with one) | `/grc-engineer:optimize-multi-framework` |
| Continuous monitoring with Slack, PagerDuty, or email alerts | `/grc-engineer:monitor-continuous` |
| Check pipeline health: which connectors are configured, last-run, cache freshness | `/grc-engineer:pipeline-status` |
| Review a PR for compliance regressions before merge | `/grc-engineer:review-pr` |
| Build audit workpapers and evidence packages | `/grc-auditor:generate-workpaper`, `/grc-engineer:collect-evidence` |
| Generate OSCAL SSP, SAP, SAR, or POA&M from findings and framework configs | `/oscal:*` (see OSCAL plugin) |
| Analyze a vendor security questionnaire (SIG, CAIQ, Yardstick) | `/grc-tprm:analyze-questionnaire` |

Every command's reference page lives in its plugin's `commands/` directory with full input and output documentation.

## Plugin categories

### Engineering hub

| Plugin | Purpose |
|---|---|
| **grc-engineer** | Where the pipeline lives: `/gap-assessment`, `scan-iac`, `test-control`, `generate-implementation`, `generate-policy`, `map-controls-unified`, `find-conflicts`, `optimize-multi-framework`, `monitor-continuous`, `collect-evidence`, `review-pr`, `pipeline-status`. |

### Persona plugins

| Plugin | Namespace | Who it's for |
|---|---|---|
| **grc-auditor** | `/grc-auditor:` | External or internal auditors: evidence review, workpaper generation, control validation |
| **grc-internal** | `/grc-internal:` | Internal GRC teams: risk registers, policy lifecycle, cert portfolio tracking |
| **grc-tprm** | `/grc-tprm:` | Third-party risk: vendor assessments, questionnaire analysis, risk scoring |

### Framework plugins

A reference layer: control IDs, families, implementation guidance, evidence patterns, and framework-specific workflow commands. Normative control text is not reproduced. Consult your licensed copy of the standard for authoritative requirements.

| Plugin | Namespace | Scope |
|---|---|---|
| **soc2** | `/soc2:` | SOC 2 TSC (Security, Availability, Confidentiality, Processing Integrity, Privacy) |
| **nist-800-53** | `/nist:` | NIST 800-53 Rev 5 (Low, Moderate, High baselines) plus overlays |
| **iso27001** | `/iso:` | ISO/IEC 27001:2022 ISMS and Annex A controls |
| **fedramp-rev5** | `/fedramp-rev5:` | FedRAMP Rev 5 (SSP, SAP, SAR, POA&M workflow) |
| **fedramp-20x** | `/fedramp-20x:` | FedRAMP 20X automated ATO with KSIs. Auto-syncs from `FedRAMP/docs`. |
| **pci-dss** | `/pci-dss:` | PCI DSS v4.0.1 including March 2025 mandatory requirements |
| **cmmc** | `/cmmc:` | CMMC 2.0 (Levels 1 to 3) |
| **hitrust** | `/hitrust:` | HITRUST CSF (i1, r2, e1) |
| **cis-controls** | `/cis:` | CIS Controls v8 (IG1, IG2, IG3). See the plugin's [LICENSE-CIS.md](plugins/frameworks/cis-controls/LICENSE-CIS.md). |
| **gdpr** | `/gdpr:` | GDPR (DPIA, breach process, data subject rights) |
| **csa-ccm** | `/ccm:` | Cloud Security Alliance Cloud Controls Matrix and CAIQ |
| **nydfs** | `/nydfs:` | NYDFS 23 NYCRR 500 |
| **dora** | `/dora:` | EU DORA (effective Jan 2025) |
| **stateramp** | `/stateramp:` | State and local government cloud authorization |
| **essential8** | `/essential8:` | Australian Essential 8 and maturity levels |
| **glba** | `/glba:` | GLBA Safeguards and Privacy Rules |
| **us-export** | `/us-export:` | ITAR and EAR combined. **Engineering guidance only, not legal advice.** Export-control determinations come from DDTC and BIS; confirm postures with counsel. |
| **pbmm** | `/pbmm:` | Canadian Protected B cloud (ITSG-33) |
| **ismap** | `/ismap:` | Japanese ISMAP gov cloud |
| **irap** | `/irap:` | Australian IRAP gov cloud (ISM and Essential Eight) |

### Connector plugins

Thin integration plugins that wrap an external inspector tool and emit findings matching [`schemas/finding.schema.json`](schemas/finding.schema.json). External tools install separately. `/<tool>:setup` handles install and credential verification.

Tier 1, ship-ready:

| Plugin | Wraps | Coverage |
|---|---|---|
| **aws-inspector** | `aws` CLI | IAM (root MFA, password policy, access keys), S3, EBS, RDS, CloudTrail, VPC, Security Hub, Config |
| **gcp-inspector** | `gcloud` CLI | IAM, Cloud Storage, Compute, Audit Logs, Security Command Center |
| **github-inspector** | `gh` CLI | branch protections, Actions, secret scanning, deploy keys, Dependabot |
| **okta-inspector** | direct Okta REST API | authentication policies, MFA, session, password, admin factor enrollment |

Tier 2, roadmap: azure, slack, duo, zoom, webex, salesforce, datadog, splunk, sumologic, newrelic, elastic, tenable, qualys, veracode, crowdstrike, paloalto, zscaler, snowflake, box, servicenow, pagerduty, zendesk, launchdarkly, mulesoft, knowbe4.

Want a connector that isn't here? [Contribute one](docs/CONTRIBUTING.md). A typical wrapper is around 200 lines.

### OSCAL / FedRAMP plugins

| Plugin | Purpose |
|---|---|
| **oscal** | Wraps [`ethanolivertroy/oscal-cli`](https://github.com/ethanolivertroy/oscal-cli) (Go rewrite of NIST's oscal-cli): `/oscal:validate`, `/oscal:convert`, `/oscal:ssp-export` |
| **fedramp-ssp** | Wraps [`ethanolivertroy/frdocx-to-froscal-ssp`](https://github.com/ethanolivertroy/frdocx-to-froscal-ssp): DOCX SSP to OSCAL 1.2.0 SSP conversion |
| **compliance-trestle** | Wraps [`ethanolivertroy/compliance-trestle-skills`](https://github.com/ethanolivertroy/compliance-trestle-skills) for OSCAL authoring (roadmap) |
| **fedramp-docs** | MCP integration with [`ethanolivertroy/fedramp-docs-mcp`](https://github.com/ethanolivertroy/fedramp-docs-mcp) for live FedRAMP documentation search (roadmap) |
| **vanta-bridge** | `/vanta:export-evidence` using `vanta-go-export` to pull Vanta evidence and normalize to Findings (roadmap) |

## The data contract

Every connector emits Findings matching [`schemas/finding.schema.json`](schemas/finding.schema.json). One Finding is one resource with one or more control evaluations. Example:

```json
{
  "schema_version": "1.0.0",
  "source": "github-inspector",
  "source_version": "0.4.1",
  "run_id": "01HXKJTSZPWX7Z1T8E8RQRN5QX",
  "collected_at": "2026-04-13T15:04:05Z",
  "resource": {
    "type": "github_repository",
    "id": "acme/prod-api",
    "uri": "https://github.com/acme/prod-api"
  },
  "evaluations": [
    {
      "control_framework": "SCF",
      "control_id": "CHG-02",
      "status": "fail",
      "severity": "high",
      "message": "Main branch has no protection rule. Direct pushes allowed.",
      "remediation": {
        "summary": "Enable branch protection on main with required reviews.",
        "ref": "grc-engineer://generate-implementation/change_management/github",
        "effort_hours": 0.25,
        "automation": "auto_fixable"
      }
    }
  ]
}
```

The contract is versioned. Consumers pin a major version. Contract tests run in CI for every connector.

## How the crosswalk works

SCF is the canonical control vocabulary. 1,468 SCF controls map bidirectionally to 249 frameworks. When a connector reports an SCF control failure, `/gap-assessment` expands it into every requested framework via the crosswalk.

SCF data is fetched from [`hackidle.github.io/scf-api`](https://hackidle.github.io/scf-api/) and cached locally. Licensing (CC BY-ND 4.0): I fetch and redistribute verbatim, never modify. See [`docs/SCF-ATTRIBUTION.md`](docs/SCF-ATTRIBUTION.md).

## Documentation

- [`docs/QUICKSTART.md`](docs/QUICKSTART.md): zero to first gap report in 10 minutes
- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md): pipeline model, plugin categories, extensibility
- [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md): add a connector, improve a framework plugin
- [`docs/SCF-ATTRIBUTION.md`](docs/SCF-ATTRIBUTION.md): SCF licensing and usage
- [`docs/ENTERPRISE-DEPLOYMENT.md`](docs/ENTERPRISE-DEPLOYMENT.md): AWS Bedrock and Google Vertex AI configuration
- [`schemas/finding.schema.json`](schemas/finding.schema.json): the data contract

## Enterprise deployment

The toolkit runs on any Claude Code model backend. For enterprise data residency or compliance requirements, point Claude Code at AWS Bedrock or Google Vertex AI:

```bash
# AWS Bedrock (FedRAMP High available in GovCloud)
export CLAUDE_CODE_USE_BEDROCK=1
export AWS_REGION=us-east-1

# Google Vertex AI (FedRAMP High as of October 2025)
export CLAUDE_CODE_USE_VERTEX=1
export CLOUD_ML_REGION=us-east5
export ANTHROPIC_VERTEX_PROJECT_ID=your-project-id
```

Full guide: [`docs/ENTERPRISE-DEPLOYMENT.md`](docs/ENTERPRISE-DEPLOYMENT.md).

## Related tools I built

This toolkit is the integration layer over a bunch of adjacent tools I maintain. Each can be used on its own:

- **Inspector family** ([`hackIDLE`](https://github.com/hackIDLE)): 30+ multi-framework compliance audit tools for AWS, Azure, GCP, OCI, Cloudflare, GitHub, Okta, Duo, Slack, Zoom, Webex, Salesforce, Snowflake, Datadog, Splunk, Sumologic, NewRelic, Elastic, Tenable, Qualys, Veracode, CrowdStrike, Palo Alto, Zscaler, KnowBe4, Box, ServiceNow, PagerDuty, Zendesk, LaunchDarkly, MuleSoft.
- **OSCAL tooling** ([`ethanolivertroy`](https://github.com/ethanolivertroy)): `oscal-cli` (Go rewrite of NIST's Java tool), `compliance-trestle-skills`, `frdocx-to-froscal-ssp`, `fedramp-docs-mcp`, `emass-skills`.
- **FedRAMP audit scripts**: cloud-shell-audit tools for AWS, Azure, and GCP, plus `duo-audit`, `slack-audit`, `splunk-audit`, `crypto-widget-evidence-collector`.
- **Policy-as-code**: `terrascan` (fork), `llm-cloudpolicy-scanner`, `wilma` (AWS Bedrock security), `mesh-security`, `mesh-config-analyzer`.
- **Reference datasets**: [`scf-api`](https://github.com/hackIDLE/scf-api) (this toolkit's crosswalk backbone), `NIST-CMVP-API`, `cmvp-tui`, `kevs-tui`, `fedramp-browser`.
- **Curated lists**: [`awesome-grc-engineering`](https://github.com/ethanolivertroy/awesome-grc-engineering), [`awesome-grc-ai`](https://github.com/ethanolivertroy/awesome-grc-ai).

## Status

Pre-1.0. The schema is versioned (v1.0.0) and Tier-1 connectors are the focus of the early releases. Breaking changes are documented in `CHANGELOG.md` and guarded by the schema version.

## License

MIT for original code. Exceptions are documented in [LICENSE](LICENSE). One plugin (`plugins/frameworks/cis-controls/`) is CC BY-SA 4.0.

## Contributing

Contributions welcome. Start with [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md). The highest-value contributions are new connectors and improvements to existing ones.
