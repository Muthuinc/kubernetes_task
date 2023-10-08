## KUBERNETES - TASK

#### Task -1 Explain how to identify the need for scaling based on traffic metrics or other indicators.

 - **Importance of monitoring:**  Monitoring helps you spot any issues before they become big problems, it is like we continuously look at something whether it is working as per our requirement or not. It also give the insights about our resourses ,so we don't ran out of it or we don't use unneccessary.  
 -  We deploy our application for some purpose. we want it to be running continuouly as long as we need it. _For eg:_ An e-commerce website should always be available to the users for the business. 
 - There are a lot of parameters available for monitoring purpose depends upon the nature of the application. Among them **Application response time** tells how fast our application is running for the user queries. It plays a vital role when we run multiple applications in a single server.
 - We allocate the _CPU and Memory_ of the server to all the available application, so when more user request accumulates at one application it's response time will be longer because the application is already consuming it's allocated maximum resources.  

 - In that case the Monitoring the _responsetime_ for the particular application tells us it needs to be scaled up (horizontaly or vertically). **we can set up a threshold value to the metrics _(CPU or Memory usage)_ as our wish (It depends upon the application and available resouces)**

 - If the CPU or Memory usage goes high beyond the threshold value for a prolonged time, it will affects the application runing in that server. 
 - **Based on that we can make decisions to increase our resource capacity through scaling**
 - Also Monitoring help us to identify the peak hours of our application usage thus enable to make data driven decision for scaling purposes.

 ---

#### Task 2: Describe the process of creating or updating Terraform code to adjust the desired replica count of the application.

 - Maintaining the Infrastructure as a code in terraform is an effective way to track and modify the resource.
 - Below is the example of deployment.tf 

 ```
 terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}


provider "aws" {
  region = "ap-south-1"
}

 resource "kubernetes_deployment" "example" {
  metadata {
    name = "terraform-example"
    labels = {
      test = "sample-app"
    }
  }

  spec {
    replicas = var.replica_count

    selector {
      match_labels = {
        test = "sample-app"
      }
    }

    template {
      metadata {
        labels = {
          test = "sample-app"
        }
      }

      spec {
        container {
          image = var.container_image
          name  = var.container_name

          resources {
            limits = {
              cpu    = var.cpu_limit
              memory = var.memory_limit
            }
            requests = {
              cpu    = var.cpu_request
              memory = var.memory_request
            }
          }
        }
      }
    }
  }
}
```
 - Variables.tf file 
 ```
 variable "replica_count" {
  description = "Number of replicas for the Kubernetes Deployment"
  type        = number
  default     = 2
}

variable "container_image" {
  description = "Container image for the Kubernetes Deployment"
  type        = string
  default     = "nginx:latest"
}

variable "container_name" {
  description = "Name of the container"
  type        = string
  default     = "sample-app"
}

variable "cpu_limit" {
  description = "CPU limit for the container"
  type        = string
  default     = "10m"
}

variable "memory_limit" {
  description = "Memory limit for the container"
  type        = string
  default     = "512Mi"
}

variable "cpu_request" {
  description = "CPU request for the container"
  type        = string
  default     = "250m"
}

variable "memory_request" {
  description = "Memory request for the container"
  type        = string
  default     = "50Mi"
} 
```
 - Below terraform commands will check and apply our resources
```
terraform init && terraform plan
terraform apply --auto-approve
```
 - In the above configuration we have 2 replicas and resouce limit of the sample application.  

 - Based on the monitoring scenario mentioned in task-1 or depends upon our needs edit the variables.tf file. **For eg:- increase or decrease the variable "replica_count" to scale up or scale down the pods**  
``` 
terraform apply --auto-approve
```
 - It will apply the configurations.

---

#### Task 3: Provide guidelines for testing the scaling changes and deploying them to the Kubernetes cluster while minimizing downtime.
 - Consider we have a staging environment similar to production k8 cluster
 - Open the variables.tf file and find the replica_count variable. ncrease or decrease the value based on your scaling needs.
 - Then do terraform ini terraform plan terraform apply --auto-approve one by one.  
 - Monitor the kubernetes cluster during and after applying changes. Always ensure our application is up & running. 
 - Example rolling update strategy
 ```
 resource "kubernetes_deployment" "example" {
  metadata {
    name = "terraform-example"
    labels = {
      test = "sample-app"
    }
  }

  spec {
    replicas = var.replica_count

    strategy {
      type = "RollingUpdate"

      rolling_update {
        max_surge       = 1
        max_unavailable = 1
      }
    }

    selector {
      match_labels = {
        test = "sample-app"
      }
    }

    template {
      metadata {
        labels = {
          test = "sample-app"
        }
      }

      spec {
        container {
          image = var.container_image
          name  = var.container_name

          resources {
            limits = {
              cpu    = var.cpu_limit
              memory = var.memory_limit
            }
            requests = {
              cpu    = var.cpu_request
              memory = var.memory_request
            }
          }
        }
      }
    }
  }
}
```
 - It is recommend to plan deployments during periods of lower application usage if possible & make sure to prepare for the rollback if it doesn't go well.
 - Make sure the application is performs as intended.
 - Scaling up by modifying the terraform configuration requires manual process. There are other scaling strategies like Horizontal and Vetical pod autoscaler resources are available in k8's
 ```
 apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: sample-app
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: sample-app
  minReplicas: 2
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```
 - It will automatically scale up and down the pods in kubernetes. 
 - In staging environment testing like these gives us an idea about what to bring to the production environment. As production is very important, we do the best there.
 
#### Overall Scaling is necessary when our application get's more popular or we run application on capped resources. Monitoring provides information about the performance and to manage our resource efficiently. Make sure we tested it out in the staging environment and prepare a rollback strategy when doing so in production.

