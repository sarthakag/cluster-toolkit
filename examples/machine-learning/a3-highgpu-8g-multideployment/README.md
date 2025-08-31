# A3 High Blueprints

## A3-High Slurm Cluster Deployment

This document will guide you to successfully provisioning a Slurm cluster with
a3-highgpu-8g compute nodes running NVIDIA H100 GPUs using a multi-deployment
group solution.

### Build the Cluster Toolkit gcluster binary

Follow instructions
[here](https://cloud.google.com/cluster-toolkit/docs/setup/configure-environment),
on how to set up your cluster toolkit environment, including enabling necessary
APIs and IAM permissions.

### (Optional, but recommended) Create a GCS Bucket for storing terraform state

```bash
#!/bin/bash
TF_STATE_BUCKET_NAME=<your-bucket>
PROJECT_ID=<your-gcp-project>
REGION=<your-preferred-region>

gcloud storage buckets create gs://${TF_STATE_BUCKET_NAME} \
    --project=${PROJECT_ID} \
    --default-storage-class=STANDARD --location=${REGION} \
    --uniform-bucket-level-access
gcloud storage buckets update gs://${TF_STATE_BUCKET_NAME} --versioning
```

### Create/modify the deployment file with your preferred configuration

Create a `a3high-slurm-deployment.yaml` file with your preferred configuration.
You can use the provided `a3high-slurm-deployment.yaml` as a template.

```yaml
---
terraform_backend_defaults:
  type: gcs
  configuration:
    bucket: <your-bucket>

vars:
  project_id:  <your-gcp-project>
  deployment_name: slurm-a3
  region: <your-preferred-region>
  zone: <your-preferred-zone>
  a3_static_cluster_size: 32
  a3_reservation_name: a3-reservation-0
```

### Deploy the cluster

Deploy the cluster using the following command:

```bash
#!/bin/bash
./gcluster deploy -d a3high-slurm-deployment.yaml a3high-slurm-blueprint.yaml --auto-approve
```

```