FEDE2asd

# Standard Platform - Terraform Module

<div align="center">

[![Standard Platform](https://img.shields.io/badge/Standard-Platform-blue?style=for-the-badge&logoColor=white)](https://gocloud.la)
[![AWS Partner](https://img.shields.io/badge/AWS%20Partner-Advanced-orange?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com/partners/find/)
[![Terraform](https://img.shields.io/badge/Terraform-Module-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)](https://www.terraform.io/)
[![GitHub](https://img.shields.io/badge/GitHub-Repository-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/gocloudLa)
[![License](https://img.shields.io/badge/License-Apache%202.0-green?style=for-the-badge&logo=apache&logoColor=white)](LICENSE)

</div>

Welcome to the Standard Platform — a suite of reusable and production-ready Terraform modules purpose-built for AWS environments.
Each module encapsulates best practices, security configurations, and sensible defaults to simplify and standardize infrastructure provisioning across projects.

## 📦 Module: Terraform ACM SSL Module
The Terraform Wrapper for ACM simplifies the configuration of the SSL Certificate Service in the AWS cloud. This wrapper functions as a predefined template, facilitating the creation and management of ACM by handling all the technical details.

### ✨ Features



### 🔗 External Modules
| Name | Version |
|------|------:|
| [terraform-aws-modules/rds-aurora/aws](https://github.com/terraform-aws-modules/terraform-aws-rds-aurora) | 9.9.1 |
| [terraform-aws-modules/security-group/aws](https://github.com/terraform-aws-modules/terraform-aws-security-group) | ~> 4.0 |
| [terraform-aws-modules/lambda/aws](https://github.com/terraform-aws-modules/terraform-aws-lambda) | 4.7.1 |



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