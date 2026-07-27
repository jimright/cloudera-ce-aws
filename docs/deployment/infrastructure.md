<!-- Copyright 2026 Cloudera, Inc.

     Licensed under the Apache License, Version 2.0 (the "License");
     you may not use this file except in compliance with the License.
     You may obtain a copy of the License at

         https://www.apache.org/licenses/LICENSE-2.0

     Unless required by applicable law or agreed to in writing, software
     distributed under the License is distributed on an "AS IS" BASIS,
     WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     See the License for the specific language governing permissions and
     limitations under the License. -->

# Infrastructure

The infrastructure playbook provisions all AWS resources using Terraform.

## What It Creates

- **VPC** with public and private subnets
- **Security groups** for SSH, reverse proxy, and Knox gateway
- **EC2 instances** across the cluster topology:
    - 1 Gateway (public subnet, Elastic IP)
    - 1 Services host (FreeIPA, PostgreSQL, pgAdmin)
    - 3 Masters
    - 4 Workers
    - 1 Cloudera Manager host
    - 1 SDX/Knox host
- **SSH keypair** (generated or user-provided)
- **Ansible inventory** via Terraform's ansible provider

## Run

```bash
ansible-navigator run playbooks/infrastructure.yml -e @config.yml
```

## Tags

| Tag | Purpose |
|-----|---------|
| `infra` | All infrastructure tasks |

## Outputs

After running:

- `tf_cluster_aws/terraform.tfvars` — generated Terraform variable file
- `<name_prefix>-ssh-key.pem` — generated SSH private key (if not using an existing key)
- `<name_prefix>-ssh.config` — SSH config for jump host access

## Customizing Node Sizing

Override instance types and counts in `config.yml`:

```yaml
infra:
  nodes:
    gateway:     { count: 1, instance_type: "t3a.medium",  root_volume_size: 100 }
    services:    { count: 1, instance_type: "t3a.large",   root_volume_size: 500 }
    masters:     { count: 3, instance_type: "t3a.xlarge",  root_volume_size: 250 }
    workers:     { count: 4, instance_type: "t3a.xlarge",  root_volume_size: 250 }
    cms:         { count: 1, instance_type: "r5a.xlarge",  root_volume_size: 300 }
    sdx:         { count: 1, instance_type: "t3a.xlarge",  root_volume_size: 500 }
```
