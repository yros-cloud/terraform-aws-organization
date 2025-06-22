# terraform-aws-organization

## This module is part of the yros-cloud-blueprints for cloud foundation

## AWS Well Architected Links reference:

https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous.html


A Terraform module to manage an AWS Organization, including:

* Creating a new AWS Organization or using an existing one
* Creating Organizational Units (OUs)
* Creating AWS accounts under the specified OUs
* Managing service access principals, policy types, and tags

---

## 🚀 Features

* Supports both creating a new organization or using an existing one via `existing_organization_id`
* Automatically creates multiple Organizational Units
* Creates AWS accounts assigned to their respective OUs
* Flexible configuration for AWS service access principals and policy types
* Adds consistent tagging across all created accounts

---

## 📦 Usage

```hcl
module "organization" {
    
  source = "yros-cloud/organization/aws"
  
  # Uncomment and set this if you already have an AWS Organization
  # existing_organization_id = "r-xxxx"

  # You can add more OUs here, such as "security", "networking", "platform", etc.
  organizational_units = {
    workloads      = "Workloads"
    infrastructure = "Infrastructure"
    # security     = "Security"
    # networking   = "Networking"
    # platform     = "Platform"
  }

  accounts = {
    development = {
      email      = "youremailgroup+awsdev@example.com"
      name       = "Development"
      parent_key = "workloads"
    }

    staging = {
      email      = "youremailgroup+awsstaging@example.com"
      name       = "Staging"
      parent_key = "workloads"
    }

    production = {
      email      = "youremailgroup+awsproduction@example.com"
      name       = "Production"
      parent_key = "workloads"
    }

    shared = {
      email      = "youremailgroup+awsshared@example.com"
      name       = "Shared"
      parent_key = "infrastructure"
    }
  }

  tags = {
    ManagedBy   = "Terraform"
    Environment = "OrgSetup"
  }

  aws_service_access_principals = [
    "sso.amazonaws.com",
    "health.amazonaws.com"
  ]

  enabled_policy_types = [
    "SERVICE_CONTROL_POLICY"
  ]

  feature_set = "ALL"
}
```

---

## 🔧 Inputs

| Name                            | Description                                                                          | Type           | Default                                                                          |
| ------------------------------- | ------------------------------------------------------------------------------------ | -------------- | -------------------------------------------------------------------------------- |
| `existing_organization_id`      | If provided, uses an existing AWS Organization root ID instead of creating a new one | `string`       | `null`                                                                           |
| `organizational_units`          | Map of keys and names for OUs to be created                                          | `map(string)`  | **required**                                                                     |
| `accounts`                      | Map of AWS accounts and their corresponding OU (`parent_key`)                        | `map(object)`  | **required**                                                                     |
| `tags`                          | Common tags to apply to each AWS account                                             | `map(string)`  | `{}`                                                                             |
| `aws_service_access_principals` | AWS service principals to enable for the Organization                                | `list(string)` | `["sso.amazonaws.com", "health.amazonaws.com", "tagpolicies.tag.amazonaws.com"]` |
| `enabled_policy_types`          | List of policy types to enable (e.g., `SERVICE_CONTROL_POLICY`)                      | `list(string)` | `["SERVICE_CONTROL_POLICY", "TAG_POLICY"]`                                       |
| `feature_set`                   | Organization feature set (`ALL` or `CONSOLIDATED_BILLING`)                           | `string`       | `"ALL"`                                                                          |

---

## 📄 Outputs

| Name                   | Description                                                         |
| ---------------------- | ------------------------------------------------------------------- |
| `accounts_summary`     | Summary of all created accounts and their corresponding OUs and IDs |
| `organizational_units` | Map of created OU keys to their AWS Organization Unit IDs           |
| `organization_id`      | The ID of the AWS Organization (existing or newly created)          |
| `root_id`              | The root ID of the AWS Organization                                 |

---

## 🧐 Notes

* If `existing_organization_id` is set, the module uses that organization and **skips creating the root account**.
* Accounts are only created if their `parent_key` matches a valid OU or `"root"`.

---

## 📘 Example

Check the [examples/basic](./examples/basic) folder for a full working example.

---

## 📝 License

MIT — see [LICENSE](./LICENSE) file.

