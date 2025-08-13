# Standard Platform - Terraform Module 🚀🚀
<p align="right"><a href="https://partners.amazonaws.com/partners/0018a00001hHve4AAC/GoCloud"><img src="https://img.shields.io/badge/AWS%20Partner-Advanced-orange?style=for-the-badge&logo=amazonaws&logoColor=white" alt="AWS Partner"/></a><a href="LICENSE"><img src="https://img.shields.io/badge/License-Apache%202.0-green?style=for-the-badge&logo=apache&logoColor=white" alt="LICENSE"/></a></p>

Welcome to the Standard Platform — a suite of reusable and production-ready Terraform modules purpose-built for AWS environments.
Each module encapsulates best practices, security configurations, and sensible defaults to simplify and standardize infrastructure provisioning across projects.

## 📦 Module: Terraform ACM SSL Module VERSION2
<p align="right"><a href="https://github.com/gocloudLa/terraform-aws-wrapper-service1/releases/latest"><img src="https://img.shields.io/github/v/release/gocloudLa/terraform-aws-wrapper-service1.svg?style=for-the-badge" alt="Latest Release"/></a><a href=""><img src="https://img.shields.io/github/last-commit/gocloudLa/terraform-aws-wrapper-service1.svg?style=for-the-badge" alt="Last Commit"/></a><a href="https://registry.terraform.io/modules/gocloudLa/wrapper-service1/aws"><img src="https://img.shields.io/badge/Terraform-Registry-7B42BC?style=for-the-badge&logo=terraform&logoColor=white" alt="Terraform Registry"/></a></p>
The Terraform Wrapper for ACM simplifies the configuration of the SSL Certificate Service in the AWS cloud. This wrapper functions as a predefined template, facilitating the creation and management of ACM by handling all the technical details.

### ✨ Features

- 🛡️ [Web Application Firewall](#web-application-firewall) - Configures WAF rules and automatically attaches WebACL to ALB listeners

- 🌐 [DNS Record](#dns-record) - Registers a CNAME DNS record in a Route53 hosted zone

- 📄 [Access Log](#access-log) - Create S3 bucket and configure LoadBalancer access log in S3



### 🔗 External Modules
| Name | Version |
|------|------:|
| [terraform-aws-modules/eventbridge/aws](https://github.com/terraform-aws-modules/eventbridge-aws) | 4.1.0 |
| [terraform-aws-modules/lambda/aws](https://github.com/terraform-aws-modules/lambda-aws) | 8.0.1 |
| [terraform-aws-modules/rds/aws](https://github.com/terraform-aws-modules/rds-aws) | 6.12.0 |
| [terraform-aws-modules/s3-bucket/aws](https://github.com/terraform-aws-modules/s3-bucket-aws) | 4.11.0 |
| [terraform-aws-modules/security-group/aws](https://github.com/terraform-aws-modules/security-group-aws) | 5.3.0 |
| [terraform-aws-modules/ssm-parameter/aws](https://github.com/terraform-aws-modules/ssm-parameter-aws) | 1.1.2 |



## 🚀 Quick Start
```hcl
acm_parameters = {
    "${local.zone_public}" = {
      subject_alternative_names = [
        "*.${local.zone_public}"
      ]
    }

      "gcl-example.com" = {
      subject_alternative_names = [
        "*.gcl-example.com"
      ]
      # EXTERNAL DNS SERVER
      create_route53_records = false
      validate_certificate   = false
    }
  }
  }

  acm_defaults = var.acm_defaults
```


## 🔧 Additional Features Usage

### Web Application Firewall
Perform the creation and configuration of WAF rules (WebAcl) as requested in the configuration, the new WebAcl generated is attached by default to the listeners used by the Amazon ALB service.


<details><summary>Configuration Code</summary>

```hcl
alb_parameters = {
  "external-00" = {
    ...
    waf_logging_enable    = true
    waf_logging_filter    = {} # Log All events (default only COUNT & BLOCK)
    # waf_logging_retention =  # Default 7 days
    waf_rules = [
      {
        name     = "AWSManagedRulesCommonRuleSet-rule-1"
        priority = "10"

        override_action = "none"

        visibility_config = {
          metric_name = "AWSManagedRulesCommonRuleSet-metric"
        }

        managed_rule_group_statement = {
          name        = "AWSManagedRulesCommonRuleSet" //WCU 700
          vendor_name = "AWS"
          rule_action_overrides = [
            {
              name = "SizeRestrictions_Cookie_HEADER"
              action_to_use = { count = {} }
            },
            {
              name = "SizeRestrictions_BODY"
              action_to_use = { count = {} }
            },
            {
              name = "EC2MetaDataSSRF_BODY"
              action_to_use = { count = {} }
            },
            {
              name = "EC2MetaDataSSRF_COOKIE"
              action_to_use = { count = {} }
            },
            {
              name = "EC2MetaDataSSRF_URIPATH"
              action_to_use = { count = {} }
            },
            {
              name = "EC2MetaDataSSRF_QUERYARGUMENTS"
              action_to_use = { count = {} }
            },
            {
              name = "CrossSiteScripting_BODY"
              action_to_use = { count = {} }
            },
            {
              name = "NoUserAgent_HEADER"
              action_to_use = { count = {} }
            },
            {
              name = "SizeRestrictions_QUERYSTRING"
              action_to_use = { count = {} }
            },
            {
              name = "GenericLFI_BODY"
              action_to_use = { count = {} }
            },
            {
              name = "GenericRFI_BODY"
              action_to_use = { count = {} }
            }
          ]
        }
      },
      {
        name     = "AWSManagedRulesKnownBadInputsRuleSet-rule-2"
        priority = "20"

        override_action = "none"

        visibility_config = {
          metric_name = "AWSManagedRulesKnownBadInputsRuleSet-metric"
        }

        managed_rule_group_statement = {
          name        = "AWSManagedRulesKnownBadInputsRuleSet" //WCU 200
          vendor_name = "AWS"
          rule_action_overrides = [
            {
              name = "PROPFIND_METHOD"
              action_to_use = { count = {} }
            },
            {
              name = "Log4JRCE"
              action_to_use = { count = {} }
            }
          ]
        }
      },
      {
        name     = "AWSManagedRulesSQLiRuleSet-rule-3"
        priority = "30"

        override_action = "none"

        visibility_config = {
          metric_name = "AWSManagedRulesSQLiRuleSet-metric"
        }

        managed_rule_group_statement = {
          name        = "AWSManagedRulesSQLiRuleSet" //WCU 200
          vendor_name = "AWS"
          rule_action_overrides = [
            {
              name = "SQLi_BODY"
              action_to_use = { count = {} }
            }
          ]
        }
      },
      {
        name     = "AWSManagedRulesLinuxRuleSet-rule-4"
        priority = "40"

        override_action = "none"

        visibility_config = {
          metric_name = "AWSManagedRulesLinuxRuleSet-metric"
        }

        managed_rule_group_statement = {
          name        = "AWSManagedRulesLinuxRuleSet" //WCU 700
          vendor_name = "AWS"

        }
      },
      {
        name     = "AWSManagedRulesAmazonIpReputationList-rule-5"
        priority = "50"

        override_action = "none"

        visibility_config = {
          metric_name = "AWSManagedRulesAmazonIpReputationList-metric"
        }

        managed_rule_group_statement = {
          name        = "AWSManagedRulesAmazonIpReputationList" //WCU 25
          vendor_name = "AWS"
        }
      },
      {
        name     = "AWSManagedRulesAnonymousIpList-rule-6"
        priority = "60"

        override_action = "none"

        visibility_config = {
          metric_name = "AWSManagedRulesAnonymousIpList-metric"
        }

        managed_rule_group_statement = {
          name        = "AWSManagedRulesAnonymousIpList" //WCU 50
          vendor_name = "AWS"
          rule_action_overrides = [
            {
              name = "HostingProviderIPList"
              action_to_use = { count = {} }
            }
          ]
        }
      },
    ]
    ...
  }
}
```


</details>


### DNS Record
Register a CNAME DNS record in a Route53 hosted zone that is present within the account, which can be public or private depending on the desired visibility type of the record.


<details><summary>Configuration Code</summary>

```hcl
dns_records = {
  "" = {
    # zone_name    = local.zone_private
    # private_zone = true
    zone_name    = local.zone_public
    private_zone = false
  }
}
```


</details>


### Access Log
Create S3 bucket and configure LoadBalancer access log in S3


<details><summary>Configuration Code</summary>

```hcl
enable_alb_logs        = true # Default: false
alb_logs_force_destroy = true # Default: false
alb_logs_lifecycle = [{
  id      = "move-to-onezone-ia"
  enabled = true
  transition = [{
    days          = 30
    storage_class = "ONEZONE_IA"
  }]
}]
```


</details>











---

## 🤝 Contributing
We welcome contributions! Please see our contributing guidelines for more details.

## 🆘 Support
- 📧 **Email**: info@gocloud.la
- 🐛 **Issues**: [GitHub Issues](https://github.com/gocloudLa/issues)

## 🧑‍💻 About
We are focused on Cloud Engineering, DevOps, and Infrastructure as Code.
We specialize in helping companies design, implement, and operate secure and scalable cloud-native platforms.
- 🌎 [www.gocloud.la](https://www.gocloud.la)
- ☁️ AWS Advanced Partner (Terraform, DevOps, GenAI)
- 📫 Contact: info@gocloud.la

## 📄 License
This project is licensed under the Apache 2.0 License - see the [LICENSE](LICENSE) file for details. 