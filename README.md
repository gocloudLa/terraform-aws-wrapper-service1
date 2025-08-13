# Standard Platform - Terraform Module 🚀🚀
<p align="right">
  <a href="https://partners.amazonaws.com/partners/0018a00001hHve4AAC/GoCloud">
    <img src="https://img.shields.io/badge/AWS%20Partner-Advanced-orange?style=for-the-badge&logo=amazonaws&logoColor=white" alt="AWS Partner"/>
  </a>
  <a href="LICENSE">
    <img src="https://img.shields.io/badge/License-Apache%202.0-green?style=for-the-badge&logo=apache&logoColor=white" alt="LICENSE"/>
  </a>
</p>

Welcome to the Standard Platform — a suite of reusable and production-ready Terraform modules purpose-built for AWS environments.
Each module encapsulates best practices, security configurations, and sensible defaults to simplify and standardize infrastructure provisioning across projects.

## 📦 Module: Terraform ACM SSL Module VERSION2
<p align="right">
  <a href="https://github.com/gocloudLa/terraform-aws-wrapper-service1/releases/latest">
    <img src="https://img.shields.io/github/v/release/gocloudLa/terraform-aws-wrapper-service1.svg?style=for-the-badge" alt="Latest Release"/>
  </a>
  <a href="">
    <img src="https://img.shields.io/github/last-commit/gocloudLa/terraform-aws-wrapper-service1.svg?style=for-the-badge" alt="Last Commit"/>
  </a>
  <a href="https://registry.terraform.io/modules/gocloudLa/wrapper-service1/aws">
    <img src="https://img.shields.io/badge/Terraform-Registry-7B42BC?style=for-the-badge&logo=terraform&logoColor=white" alt="Terraform Registry"/>
  </a>
</p>
The Terraform Wrapper for ACM simplifies the configuration of the SSL Certificate Service in the AWS cloud. This wrapper functions as a predefined template, facilitating the creation and management of ACM by handling all the technical details.

### ✨ Features

- 🔢 [Multiple Tasks](#multiple-tasks) - Supports multiple containers per service with shared Fargate resources

- 🔗 [Integration with ALB](#integration-with-alb) - Automates ALB target groups, listeners, and health checks

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



### Multiple Tasks
The module supports starting more than one container for each service.<br/>
This way, the serverless hardware that runs the containers (fargate) is shared.<br/>
**IMPORTANT** two containers running in the same service can receive requests from the same load balancer, but it is a condition that the containers run on different ports.


<details><summary>Configuration Code</summary>

```hcl
ecs_service_parameters = {
  ExDouble {
    # ecs_cluster_name                       = "dmc-prd-core-00"
    # vpc_name                               = "dmc-prd"
    # subnet_name                            = "dmc-prd-private*"
    enable_autoscaling = false

    enable_execute_command = true

    # Policies que usan la tasks desde el codigo desarrollado
    tasks_iam_role_policies   = {}
    tasks_iam_role_statements = []
    # Policies que usa el servicio para poder iniciar tasks (ecr / ssm / etc)
    task_exec_iam_role_policies = {}
    task_exec_iam_statements    = []


    containers = {
      app = {
        map_environment = {}
        map_secrets     = {}
        mount_points    = []
        ports = {
          "port1" = {
            container_port = 80
            load_balancer = {
              "alb1" = {
                alb_name = "dmc-prd-core-external-00"
                listener_rules = {
                  "rule1" = {
                    # priority          = 10
                    # actions = [{ type = "forward" }] # Default Action 
                    conditions = [
                      {
                        host_headers = ["ExDoubleEcr.${local.zone_public}"]
                      }
                    ]
                  }
                }
              }
            }
          }
        }
      }
      web = {
        map_environment = {}
        map_secrets     = {}
        mount_points    = []
        ports = {
          "port1" = {
            container_port = 81
          }
        }
      }
    }
  }
}
```


</details>


### Integration with ALB
Supports integration with ALB, automates generation of target_groups and listener_rules.<br/>
Also provides health_check features for the configured endpoints.


<details><summary>Configuration Code</summary>

```hcl
ecs_service_parameters = {
  ExAlb = {
    # ecs_cluster_name                       = "dmc-prd-core-00"
    # vpc_name                               = "dmc-prd"
    # subnet_name                            = "dmc-prd-private*"
    enable_autoscaling                 = false

    enable_execute_command = true

    # Policies que usan la tasks desde el codigo desarrollado
    tasks_iam_role_policies   = {}
    tasks_iam_role_statements = []
    # Policies que usa el servicio para poder iniciar tasks (ecr / ssm / etc)
    task_exec_iam_role_policies = {}
    task_exec_iam_statements    = []


    ecs_task_volume = []

    containers = {
      app = {
        image                 = "public.ecr.aws/docker/library/nginx:latest"
        create_ecr_repository = false
        ports = {
          "port1" = {
            container_port = 80
            # host_port      = 80    # Default: container_port
            # protocol       = "tcp" # Default: tcp
            # cidr_blocks    = [""]  # Default: [vpc_cidr]
            load_balancer = {
              "alb1" = {
                alb_name             = "dmc-prd-core-external-00"
                alb_listener_port    = 443
                deregistration_delay = 300
                slow_start           = 30
                health_check = {
                  # # Default Values
                  # path                = "/"
                  # port                = "traffic-port"
                  # protocol            = "HTTP"
                  # matcher             = 200
                  # interval            = 30
                  # timeout             = 5
                  # healthy_threshold   = 3
                  # unhealthy_threshold = 3
                }
                listener_rules = {
                  "rule1" = {
                    # priority          = 10
                    # actions = [{ type = "forward" }] # Default Action
                    conditions = [
                      {
                        host_headers = ["ExAlb.${local.zone_public}"]
                      }
                    ]
                  }
                  # REDIRECT
                  # curl -v -H 'Host: ExAlb-redirect.democorp.cloud' https://{balancer_domain}
                  "rule2" = {
                    # priority          = 10
                    actions = [{
                      type        = "redirect"
                      host        = "google.com"
                      port        = 443
                      status_code = "HTTP_301"
                    }]
                    conditions = [
                      {
                        host_headers = ["ExAlb-redirect.${local.zone_public}"]
                      }
                    ]
                  }
                  # FIXED RESPONSE
                  # curl -v -H 'Host: ExAlb-fixed.democorp.cloud' https://{balancer_domain}
                  "rule3" = {
                    # priority          = 10
                    actions = [{
                      type         = "fixed-response"
                      message_body = "Unauthorized - Fixed Response"
                      status_code  = 401
                      content_type = "text/plain"
                    }]
                    conditions = [
                      {
                        host_headers = ["ExAlb-fixed.${local.zone_public}"]
                      }
                    ]
                  }
                }
              }
            }
          }
        }
        map_environment = {}
        map_secrets     = {}
        mount_points    = []
      }
    }
  }
}
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