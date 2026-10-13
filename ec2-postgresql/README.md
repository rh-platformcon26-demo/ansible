# ec2-postgresql

Provision an AWS security group and a Fedora Server/Cloud EC2 instance, then install PostgreSQL listening on **localhost only**.

Access the database from your laptop with an SSH tunnel (port 5432 is not opened in the security group).

## Prerequisites

- Ansible 2.15+
- AWS credentials (`AWS_PROFILE` or `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY`)
- Existing **VPC**, **subnet**, and **EC2 key pair**
- Matching private key file on the control node
- Collections:

```bash
ansible-galaxy collection install -r requirements.yml
```

Python dependencies for AWS modules (control node):

```bash
pip install boto3 botocore
```

## Configuration

Copy the example vars file and fill in real values (the local copy is gitignored):

```bash
cp vars/demo.example.yml vars/demo.yml
```

Required variables:

| Variable | Description |
|---|---|
| `aws_region` | AWS region |
| `vpc_id` | Existing VPC |
| `subnet_id` | Existing subnet (prefer public if you need SSH from the internet) |
| `key_name` | Existing EC2 key pair name |
| `ansible_ssh_private_key_file` | Path to the private key |
| `postgres_password` | Application DB password |

Optional defaults live in `group_vars/all.yml` (`instance_type`, `postgres_db`, `postgres_user`, `ssh_ingress_cidr`, …).

## Run

```bash
cd ec2-postgresql
ansible-playbook playbooks/site.yml -e @vars/demo.yml
```

What the playbook does:

1. Looks up the latest Fedora Cloud AMI
2. Creates a security group with **SSH/22** only (no 5432 ingress)
3. Launches the EC2 instance and waits for SSH
4. Installs PostgreSQL, binds it to `localhost`, creates DB + user

## Connect via SSH tunnel

After the playbook finishes, use the public (or private) IP printed in the output:

```bash
ssh -i ~/.ssh/my-keypair.pem \
  -L 5432:127.0.0.1:5432 \
  fedora@<ec2_ip>
```

Then connect locally:

```bash
psql -h 127.0.0.1 -p 5432 -U appuser -d appdb
```

## CI / RHDH (later)

This playbook is non-interactive: pass all values with `-e` or `-e @file`. A future RHDH scaffolder template can clone this repo and run the same command in CI.
