# CloudOps Automation Platform — Architecture

```mermaid
flowchart TD
    DEV["Developer"]
    GH["GitHub Repository"]
    CI["GitHub Actions"]
    TF["Terraform Configuration"]
    CK["Checkov Security Scan"]
    AWS["AWS Account"]
    ART["Private S3 Artifacts Bucket"]
    LOG["Private S3 Access Logs Bucket"]
    BUD["AWS Zero Spend Budget"]

    DEV --> GH
    GH --> CI
    CI --> TF
    CI --> CK
    TF -. "Provisioning managed locally" .-> AWS
    AWS --> ART
    ART -- "Server access logs" --> LOG
    BUD -. "Cost alerts" .-> AWS

    classDef secure fill:#dcece4,stroke:#28704a,color:#163b29
    class ART,LOG,CK secure
```

## Security controls

- S3 public access is blocked on both buckets.
- The artifacts bucket uses AWS-managed KMS encryption.
- The access logs bucket uses SSE-S3 encryption.
- Versioning is enabled on both buckets.
- S3 server access logging targets the separate logs bucket.
- GitHub Actions validates Terraform and runs Checkov.
- The CI workflow does not run `terraform apply`.

## Cost controls

An AWS Zero Spend Budget is configured for spending alerts. Budget alerts are not a spending cap, and Cost Explorer data may take time to become available.
