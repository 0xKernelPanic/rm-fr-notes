---
title: "PMapper"
tags: []
created: "2025-03-16"
---

# PMapper

## Description
PMapper is a cloud security tool that focuses on AWS authorization risks (IAM).

### How PMapper Works

PMapper includes a querying mechanism that uses a local simulation of AWS's authorization behavior. When running a query to determine if a principal has access to a certain action/resource, PMapper also checks if the user or role could access other users or roles that have access to that action/resource. This catches scenarios such as when a user doesn't have permission to read an S3 object, but could launch an EC2 instance that can read the S3 object.

- Models AWS accounts as directed graphs.
- IAM Users/Roles (principals) are Nodes
- Edges are how one user/role in an account can access another
- With PMapper\`s IAM policy simulation, allows you to identify the "effective permissions" of all principals in an AWS account

![[example-viz.png]]

**Legend**:
- White - Regular nonadmin users and roles
- Blue - Admin users and roles
- Red - Any nonadmin that have an ability to gain access to an admin resulting in a potential privilege escalation risk
## Use Cases
- identifing and visualizing the different ways IAM Users and Roles can access each other 
- Determinig which users have admin-level permissions
- Detecting privilege-escalation risks
- Finding attack paths where one or more principals can be used in a chain to obtain access to a given operation or resource in an AWS account
## Commands
### Installation
```bash
git clone git@github.com:nccgroup/PMapper.git
cd PMapper
docker build -t $TAG .
docker run -it $TAG
```

You can use `-e|--env` or `--env-file` to pass the `AWS_*` environment variables for credentials when calling `docker run ...`, or use `-v` to mount your `~/.aws/` directory and use the `AWS_CONFIG_FILE` and `AWS_SHARED_CREDENTIALS_FILE` environment variables. The current Dockerfile should put you into a shell with `pmapper -h` ready to go as well as `graphviz` already installed.
## Fun Facts
- 

## References
- https://www.youtube.com/watch?v=YczbtueYkVQ&t=113s
- 
