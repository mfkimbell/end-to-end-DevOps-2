# end-to-end-DevOps-2


### **Tools Used:**
* `AWS` Hosting of all CI/CD resources and permissions to those resources
* `EC2` Manage instances that run various servers for the application architecture
* `Maven` Managing dependencies and compiling Java code
* `Jenkins` Build and monitor automation of the application deployment process
* `Ansible` Scripts for uploading new Docker Images and Kubernetes pulling of Images
* `Docker` Containerize the application and server
* `Kubernetes` Docker container management and fault tolerance with load balancer
* `EKS` AWS management of Kubernetes
* `eksctl` Command line management of EKS clusters on AWS CLI
* `kubectl` Command line management of Kubernetes clusters


### Purpose


## Documentation from start to end:

**As I will be deleting all of these AWS resources to prevent charges on my account, I have documented thoroughly this entire process.**

Install Terraform and create a PATH so it can be executed from anywhere. I simply added it to /user/local/bin.

Install AWS CLI 

Create a new IAM user and grab its credentials for the CLI

<img width="705" alt="Screenshot 2023-11-24 at 10 51 38 AM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/8e6a59dd-eb8d-458e-a17e-ed09ce2ac984">

Test command to ensure AWS CLI is working. Obviously, we could use CloudShell to connect as well. 

<img width="376" alt="Screenshot 2023-11-24 at 10 55 08 AM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/633e3332-8606-488f-b633-254ec91c4e1f">

