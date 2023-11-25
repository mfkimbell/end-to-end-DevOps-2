# terraform-aws-DevOps-pipeline


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

**Note: previously I was able to use instance connect by default cause I had been using Amazon Linux 2 EC2 Instances. This time, I had to SSH into the instance from my Bash terminal with a pem key as Amazon Linux 2 is becoming outdated soon**

Amazon Linux
```
ssh -i <path to pem>.pem ec2-user@<public IP>
```
Ubuntu
```
ssh -i <path to pem>.pem ubuntu@<public IP>
```

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

I altered the EC2 instances to dynamically create for Jenkins master, slave, and ansible

<img width="640" alt="Screenshot 2023-11-24 at 5 22 58 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/1bd75991-0a72-4450-96dc-a27201e27263">

And here we can see those running

<img width="749" alt="Screenshot 2023-11-24 at 5 31 36 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/7d807235-15a5-4aff-b8f4-c84a4ed39006">

**Roadblock that took me a couple hours to debug**
When you SSH into an **Ubuntu** instance, you need to set the user to "Ubuntu" not "ec2-user"

run the following commands to install ansible on the ubuntu server
```
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

For Jenkins-master setup, I'm going to use the private IP since the public IP will change over time. These servers are in the same VPC so connection will be fine. 

I create a "hosts" file in the ansible server that containers the jenkins master IP as well as some variables that will allow connection, a username of "ubuntu" and I'll use the dpp.pem file which i'll copy into the server.

<img width="342" alt="Screenshot 2023-11-24 at 6 12 28 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/dd8aa30c-a91d-41d1-8030-f336b1e5da01">

**Roadblock**
If you want ansible to have access to another EC2, it needs the keypair pem file. I normally program on mac and tried to use SCP command

```
scp -i dpp.pem dpp.pem ubuntu@<address>:/opt
```
to get it into my folder of interest, however, it would only do it for the base image and not for when I was logged in as root user. So as you can see, I switched to windows, downloaded MobaXTerm, and imported the file that way. 

![image](https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/da20dba3-4314-4410-877a-d9a713f8a0e7)

Now, ansible can successfully connect to the Jenkins-master

![image](https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/2b9b3b74-1699-4933-bdd1-54c988e6d55b)

I also added the slave to the hosts file 

![image](https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/c2dd8431-6d89-49bc-9bc3-617c0aff36e6)

Now, we can add an ansible playbook to our Ansible server for setting up Jenkins:

<img width="829" alt="Screenshot 2023-11-24 at 9 07 23 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/7fb434e7-a86b-4712-a0f6-f293411f9f55">

I add port 8080 to the ingress of our terraform so we can access the Jenkins server via browser, and it sucessfully loads:

<img width="531" alt="Screenshot 2023-11-24 at 9 40 10 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/5ff0dba0-156a-457b-95b9-1fb93a306b04">

Now, we want to write an ansible playbook to install maven on our jenkins-slave, which will be executing tasks queued by the jenkins-master:

<img width="762" alt="Screenshot 2023-11-24 at 9 42 52 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/e8f81334-7814-4027-9a35-cbecab35cb99">

We want our Jenkins-master to be able to access the build server, so we add the .pem credentials:

<img width="742" alt="Screenshot 2023-11-25 at 12 00 13 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/a6e1b8c2-23ba-405a-93b6-45499359fd80">

Then we create the slave node:

<img width="846" alt="Screenshot 2023-11-25 at 12 13 48 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/40e6eac1-12a9-45a6-82c9-b9f0d34ce6ea">

I create a test job and successfully execute a test command:

<img width="739" alt="Screenshot 2023-11-25 at 12 28 08 PM" src="https://github.com/mfkimbell/end-to-end-DevOps-2/assets/107063397/2c586048-5ed8-440c-995b-5e9b3e51bf28">

Now we create a new job to run the local application. We create a test pipeline script:

<img width="921" alt="Screenshot 2023-11-25 at 1 02 04 PM" src="https://github.com/mfkimbell/Terraform-AWS-CICD-Pipeline/assets/107063397/fdd5d6f5-c66d-4afa-9a5b-020ae77022ff">

Which runs successfully:

<img width="969" alt="Screenshot 2023-11-25 at 1 04 10 PM" src="https://github.com/mfkimbell/Terraform-AWS-CICD-Pipeline/assets/107063397/f2bd6d1d-d803-4efe-bd4b-45ac6a4c9feb">








