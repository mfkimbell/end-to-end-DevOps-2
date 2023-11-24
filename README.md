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

Now we create a basic terraform file for an EC2 instance

<img width="481" alt="Screenshot 2023-11-24 at 2 49 03 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/1a5be9ad-a371-41f4-9fca-6e0a041b3457">

Now we run `terraform init` `terraform validate` `terraform plan` and `terraform apply`. Which will start the EC2 instance:

<img width="749" alt="Screenshot 2023-11-24 at 2 55 33 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/5794ff20-9c17-42bd-9254-03f8491bcc45">

Note: previously I was able to use instance connect by default cause I had been using Amazon Linux 2 EC2 Instances. This time, I SSH-ed into the EC2 instance.

Additionally, we need to alter the terraform file to create a security group that will allow SSH:

<img width="439" alt="Screenshot 2023-11-24 at 3 45 41 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/e61a3cb2-c7c0-4320-96c9-0b5bfe549a85">

We can see the security group was sucessfully added

<img width="826" alt="Screenshot 2023-11-24 at 4 04 02 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/c32b2550-6fac-47c3-8c98-7103df5dc91c">

Now we continue to alter the terraform file adding a VPC

<img width="384" alt="Screenshot 2023-11-24 at 4 49 22 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/afc26505-4865-44ae-a42e-53b30b7f979b">

Two subnets in different availability zones

<img width="446" alt="Screenshot 2023-11-24 at 4 51 51 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/69da0b1c-611f-4517-868c-8df27fea3809">

An internet gateway plus a route table that connects to that gateway:

<img width="425" alt="Screenshot 2023-11-24 at 4 54 00 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/fc325f46-8703-441b-b47b-7b4921bc1c5c">

Then we need to do the route table assocaition:

<img width="582" alt="Screenshot 2023-11-24 at 5 00 30 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/93424ddf-8a19-442f-8a8c-0e95f8824889">

And we can see all these resources being formed:

<img width="698" alt="Screenshot 2023-11-24 at 5 14 19 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/b60453be-8a91-4cd2-8e0a-67c5419aa084">



