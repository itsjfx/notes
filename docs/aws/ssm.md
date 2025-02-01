# AWS Systems Manager / SSM

## Automation runbooks

Amazon offer a bunch of automation documents which can be used with AWS config to remediate resources.

https://docs.aws.amazon.com/systems-manager-automation-runbooks/latest/userguide/automation-runbook-reference.html

## Session Manager

```
session-manager-plugin csSessionData awsregion StartSession profile_name/'' https://ssm.awsregion.amazonaws.com
```
