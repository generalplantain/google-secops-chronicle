# Google SecOps (Chronicle) Detection Rules

A community collection of **38 YARA-L detection rules** for [Google Security Operations (Chronicle)](https://cloud.google.com/security/products/security-operations). These rules provide coverage across identity, email, collaboration, and source code management platforms.

## Data Sources

| Category | Rules | Covers |
|----------|-------|--------|
| [Okta](detections/okta/) | 19 | MFA resets, privilege escalation, session impersonation, phishing (FastPass), IdP modifications, suspicious logins, breakglass usage |
| [Google Workspace](detections/google-workspace/) | 12 | Admin role changes, domain-wide delegation, Gmail settings tampering, external group members, phishing reports, Drive ownership transfers |
| [GitHub](detections/github/) | 4 | Mass repo cloning, repo deletion, owner role grants, app installations |
| [1Password](detections/1password/) | 3 | Login without MFA, brute force attempts, SCIM bridge failures |

## Getting Started

### Prerequisites

- A Google SecOps (Chronicle) instance with log ingestion configured for the relevant data sources
- Appropriate permissions to create detection rules

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/generalplantain/google-secops-chronicle.git
   ```

2. Copy the `.yaral` files for your data sources into your Google SecOps detection engine, either through the UI or the [Detection Engine API](https://cloud.google.com/chronicle/docs/reference/detection-engine-api).

3. **Customise before deploying** — several rules contain placeholders you should update:
   - `/@yourdomain\.com$/` — replace with your organisation's email domain(s)
   - `%vip_users` — create a reference list with your VIP/executive user identifiers
   - `breakglass-account@example.com` — replace with your actual breakglass account
   - `%okta_environments` — create a reference list with your Okta tenant namespace(s)

## Rule Structure

Each rule follows a consistent structure:

```
rule rule_name {
    meta:        // Author, description, MITRE ATT&CK mapping, severity
    events:      // Log source filters and field matching
    match:       // Grouping / correlation window (where applicable)
    outcome:     // Risk scoring and contextual enrichment for triage
    condition:   // Trigger logic
}
```

### Risk Scoring

All rules use a standardised risk scoring model based on severity tier:

| Severity | Base Score | + Weekend | + Off-Hours | Max Score |
|----------|-----------|-----------|-------------|-----------|
| Critical | 70 | +15 | +15 | 100 |
| High | 55 | +15 | +15 | 85 |
| Medium | 40 | +15 | +15 | 70 |
| Low | 20 | +15 | +15 | 50 |

- **Weekend bonus** applies on Saturday and Sunday (UTC)
- **Off-hours bonus** applies before 08:00 or after 18:00 UTC
- Adjust the UTC offsets to match your organisation's working hours

### MITRE ATT&CK Coverage

Rules are mapped to MITRE ATT&CK tactics and techniques. Key coverage areas include:

- **Initial Access** — phishing, valid accounts, restricted country logins
- **Persistence** — account manipulation, admin role changes, IdP modifications
- **Privilege Escalation** — super admin grants, custom role modifications
- **Credential Access** — MFA resets, brute force, session impersonation
- **Defense Evasion** — policy deletion, MFA disabling
- **Collection** — email collection, data staging, mass repo cloning
- **Impact** — application deletion, repository destruction

## Repository Structure

```
detections/
  1password/          # 1Password detection rules
  github/             # GitHub audit log detection rules
  google-workspace/   # Google Workspace & Gmail detection rules
  okta/               # Okta identity detection rules
```

## Contributing

Contributions are welcome! When submitting a new rule, please ensure it:
- Follows the YARA-L structure above
- Includes a `meta` block with author, description, MITRE mapping, severity, and data source
- Uses the standardised risk scoring model
- Contains inline comments explaining the detection logic
- Has no organisation-specific references (email domains, tenant names, internal URLs)

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
