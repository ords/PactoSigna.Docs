# Cloud Tasks Setup for Auditor Export

This document describes the infrastructure setup required for the auditor export feature, which uses Cloud Tasks to process export jobs asynchronously and Cloud Storage to store the generated ZIP archives.

## Create the Cloud Tasks Queue

Run the following command to create the auditor export queue:

```bash
gcloud tasks queues create auditor-export-queue \
  --location=us-central1 \
  --max-concurrent-dispatches=5 \
  --max-dispatches-per-second=1 \
  --max-attempts=3 \
  --min-backoff=60s \
  --max-backoff=3600s
```

### Queue Configuration Explained

| Setting                     | Value | Rationale                                                                |
| --------------------------- | ----- | ------------------------------------------------------------------------ |
| `max-concurrent-dispatches` | 5     | Matches document processing concurrency to avoid overwhelming the system |
| `max-dispatches-per-second` | 1     | Rate limits task execution to prevent resource spikes                    |
| `max-attempts`              | 3     | Provides retry resilience for transient failures                         |
| `min-backoff`               | 60s   | Initial retry delay gives time for transient issues to resolve           |
| `max-backoff`               | 3600s | Maximum 1-hour delay between retries                                     |

## Create the Cloud Storage Bucket

```bash
# Create bucket
gsutil mb -l us-central1 gs://${PROJECT_ID}-auditor-exports

# Set lifecycle rule for 7-day auto-deletion
cat > /tmp/lifecycle.json << 'EOF'
{
  "rule": [{
    "action": {"type": "Delete"},
    "condition": {"age": 7}
  }]
}
EOF

gsutil lifecycle set /tmp/lifecycle.json gs://${PROJECT_ID}-auditor-exports
```

### Storage Configuration Explained

- **Location**: `us-central1` - Co-located with Cloud Functions for optimal performance
- **Lifecycle Rule**: 7-day auto-deletion ensures compliance with data retention policies and reduces storage costs
- **Bucket Naming**: Uses project ID prefix for uniqueness and easy identification

## IAM Permissions

The Cloud Functions service account needs the following roles:

| Role                                   | Purpose                                     |
| -------------------------------------- | ------------------------------------------- |
| `roles/cloudtasks.enqueuer`            | Create and manage tasks in the queue        |
| `roles/storage.objectAdmin`            | Upload export ZIPs and generate signed URLs |
| `roles/iam.serviceAccountTokenCreator` | Sign URLs for secure download links         |

```bash
PROJECT_ID=$(gcloud config get-value project)
SA_EMAIL="${PROJECT_ID}@appspot.gserviceaccount.com"

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:${SA_EMAIL}" \
  --role="roles/cloudtasks.enqueuer"

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:${SA_EMAIL}" \
  --role="roles/storage.objectAdmin"

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:${SA_EMAIL}" \
  --role="roles/iam.serviceAccountTokenCreator"
```

## Verification

After setup, verify the configuration:

```bash
# Verify queue exists
gcloud tasks queues describe auditor-export-queue --location=us-central1

# Verify bucket exists and has lifecycle rule
gsutil ls gs://${PROJECT_ID}-auditor-exports
gsutil lifecycle get gs://${PROJECT_ID}-auditor-exports

# Verify IAM bindings
gcloud projects get-iam-policy $PROJECT_ID \
  --filter="bindings.members:${SA_EMAIL}" \
  --format="table(bindings.role)"
```

## Environment Variables

Ensure the following environment variables are set in your Cloud Functions configuration:

```bash
# Required for the export feature
GCLOUD_PROJECT=your-project-id
CLOUD_TASKS_LOCATION=us-central1
AUDITOR_EXPORT_QUEUE=auditor-export-queue
AUDITOR_EXPORT_BUCKET=your-project-id-auditor-exports
```

## Troubleshooting

### Common Issues

1. **Task creation fails with permission denied**
   - Verify the service account has `roles/cloudtasks.enqueuer`
   - Check that the queue exists in the correct location

2. **Signed URL generation fails**
   - Verify `roles/iam.serviceAccountTokenCreator` is granted
   - Ensure the bucket name is correct in environment variables

3. **Export files not being deleted after 7 days**
   - Verify lifecycle rule is applied: `gsutil lifecycle get gs://${PROJECT_ID}-auditor-exports`
   - Note: Lifecycle rules are evaluated once per day, so deletion may take up to 24 hours after the 7-day mark
