# terraform-aws-DevOps-pipeline


### **Tools Used:**
* `AWS` Hosting of all CI/CD resources and permissions to those resources
* `EC2` Manage instances that run various servers for the application architecture
* `Maven` Managing dependencies and compiling Java code
* `Jenkins` Build and monitor automation of the application deployment process
* `Ansible` Scripts for establishing jenkins masters and slave servers
* `SonarQube` Automatic analysis of code for bugs and bad design
* `JFrog Artifactory` Manage build artifacts and store Docker containers
* `Docker` Containerize the application and server
* `Kubernetes` Docker container management and fault tolerance with load balancer
* `EKS` AWS management of Kubernetes
* `eksctl` Command line management of EKS clusters on AWS CLI
* `kubectl` Command line management of Kubernetes clusters


### Purpose


Jenkins Jobs running:

<img width="909" alt="Screenshot 2023-11-26 at 12 07 45 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/d647c535-45e0-40d0-b662-ffa837ff6e47">

Jenkins multi-pipeline job for branches:

<img width="759" alt="Screenshot 2023-11-26 at 12 37 13 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/2a46e17a-d885-4f69-bcb2-a6ec72ec670b">

Aws instances for jenkins, build slave, ansible, and EKS:

<img width="1024" alt="Screenshot 2023-11-26 at 12 16 03 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/32169397-a55d-48aa-8e83-8d497d67b598">

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

Now, we change the code to a Jenkinsfile on the local repository and connect it via SCM:

<img width="551" alt="Screenshot 2023-11-25 at 1 25 39 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/8a27caee-9133-48aa-bf89-6f045db757f0">

I then update the Jenkins file to build with maven:

```
stage("build"){
            steps {
                 echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                 echo "----------- build complted ----------"
            }
        }
```

Then add my github repository to the Jenkins credentials, however, this is only necessary if I make my repository private:

<img width="729" alt="Screenshot 2023-11-25 at 1 44 58 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/7f974930-46b8-484d-9751-1ed7f31ff0b7">

I created a multi-branch pipeline for the project and add a dev and stage branch:

<img width="600" alt="Screenshot 2023-11-25 at 2 53 04 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/f0b174af-3a2d-4cb6-9488-0dfbee00e3d7">


I install the `Multibranch Scan Webhook Trigger` plugin on Jenkins. I then add a webhook on github that triggers the multi-branch pipeline job.

<img width="572" alt="Screenshot 2023-11-25 at 2 36 59 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/dc7ed472-fd15-46e3-9279-a17e19014291">

<img width="595" alt="Screenshot 2023-11-25 at 2 33 57 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/9b45faca-4305-4e6b-902e-ba239860c461">

Now after a commit, we can see the webhook automatically triggers which will run the multibranch job and update the main branch, however, it will automatically make changes for whichever branch has changes made to it:

<img width="621" alt="Screenshot 2023-11-25 at 2 38 29 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/47524eb0-ee96-4ac2-8bd8-9c876a8f777e">

Now we load up SonarQube and create a security token, and add that token to Jenkins.

<img width="1145" alt="Screenshot 2023-11-25 at 6 41 02 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/38fe7428-961e-4c53-9483-c51411195acf">

Then, we add SonarQube server to Jenkins. 

<img width="725" alt="Screenshot 2023-11-25 at 6 44 02 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/06ca91c4-2511-4fe6-9033-a9d2ca8d034c">

<img width="725" alt="Screenshot 2023-11-25 at 6 45 52 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/7e4ef679-4357-4751-81d5-d713b93dbebf">

I create an "organization" on sonarcloud.io, add a project key, and then add a sonar properties file to my repo:

<img width="576" alt="Screenshot 2023-11-25 at 6 58 49 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/0f3599bf-e6f9-44be-8094-54fbe29e1ea5">

Then we add a stage to the Jenkinsfile for SonarQube analysis:

<img width="530" alt="Screenshot 2023-11-25 at 7 09 56 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/4a69639f-6fa1-4af8-b1cf-2c4b033055d4">

Now we are able to get stats about the quality of our code from SonarQube like bugs, code-smell, and others. You can add quality gates for things thigns like code-duplication or bugs, to fail the builds if thresholds are passed. I built one for 50+ bugs in the code (however sonar considers a LOT of things "bugs"), then added a "Quality Gate" stage to the Jenkins file:

<img width="799" alt="Screenshot 2023-11-25 at 9 06 49 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/511eb693-5ee7-4def-8fa8-8b804fd79670">

Now I would like to use Jfrog Artifactory to publish a Docker Image of my application. First, we create an access token on Jfrog and add it to Jenkins credentials. Also need to install the `Artifactory` plugin on Jenkins. 

<img width="873" alt="Screenshot 2023-11-25 at 10 01 05 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/a3a0449a-0b41-4999-8db0-ab45b0f2e39a">

Now, we add a stage to our Jenkinsfile to capture the `.jar` file created by our project and store it on Jfrog Artifactory. 

<img width="834" alt="Screenshot 2023-11-25 at 10 07 26 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/44d4958b-784c-4f13-ad7b-26d7c8b6c394">

**RoadBlock**
If at first you don't succeed... I ended up having a couple typos that made this frustating to figure out. 

<img width="441" alt="Screenshot 2023-11-25 at 10 19 06 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/f633b637-c34a-46ee-bcd3-f048a80989a6">

Here we can see the files successfully uploaded to JFrog:

<img width="1103" alt="Screenshot 2023-11-25 at 10 21 56 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/7e8186cc-767d-4087-ac96-a9bc4117caa7">

However, we want to deploy this as a microservice, so this needs to be deployed on Docker. So first, we need to install docker on our jenkins slave by updating the ansible playbook `jenkins-slave-setup.yaml`

<img width="507" alt="Screenshot 2023-11-25 at 10 32 34 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/792e54b4-5a0b-4666-b224-e85b64afe00d">

And it succeeded! (after some syntax changes not shown)

<img width="566" alt="Screenshot 2023-11-25 at 10 51 59 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/0028f53d-fd63-4e72-8611-4450d5620dc4">

We create a dockefile to host the jar execution:

<img width="662" alt="Screenshot 2023-11-25 at 11 02 50 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/cbe455bc-ca55-435b-a547-582649d9cc9d">

Here we can see our dockerfile in our slave after committing:

<img width="574" alt="Screenshot 2023-11-25 at 11 06 09 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/6536026a-7bf0-4796-a278-ed393fe4934a">

I create a docker repository on JFrog:

<img width="791" alt="Screenshot 2023-11-25 at 11 13 58 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/482b954a-5205-4ebc-b620-395c550cec70">

I install `docker pipeline` on Jenkins, then I add `Docker Build` and `Docker Publish` stages to the Jenkinsfile:

<img width="514" alt="Screenshot 2023-11-25 at 11 23 37 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/a5bef739-6fbb-4215-bbfd-b5fe62f7da71">

<img width="654" alt="Screenshot 2023-11-25 at 11 24 33 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/eb3adb69-a4a5-4dd1-b6d7-6001fbfbff45">

We have been blessed with no failures:

<img width="501" alt="Screenshot 2023-11-25 at 11 33 59 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/f8cfaa93-9fce-436c-abbc-071d2ba4d6d7">

<img width="737" alt="Screenshot 2023-11-25 at 11 35 07 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/4da48d0e-8373-418e-9193-8defe39ad154">

<img width="929" alt="Screenshot 2023-11-25 at 11 36 02 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/ebcbce80-6790-4b58-bb82-30a9425c6f67">

We can manually start the container now:

<img width="876" alt="Screenshot 2023-11-25 at 11 38 57 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/951c481a-7dc5-4899-acd4-8d663075a40e">

Now, after we open port 8000 on Jenkins-Slave, we can access the application:

<img width="506" alt="Screenshot 2023-11-25 at 11 43 23 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/ba9f801e-cf6f-432f-9833-a1fa56242950">

I renamed the original terraform file, since now we are going to have terraform files for eks/kubernetes as well as security group management:

<img width="260" alt="Screenshot 2023-11-26 at 12 10 26 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/d57cf65e-2823-4f26-ba9a-106e7b76f799">

I will not show these files in their entirety since they are long, however, we allocate the necessary resources for eks, policies for eks to access what it needs to, an iam role for ec2 eks worker, an autoscaling policy, and various resource policies for s3, daemon (for tracking), and other things. We have an output file for out endpoint, and we have a variable file to keep track of ids like security group, subnet, and vpc ids.

To our original vpc terraform file, we add lines to execute our other terraform files:

<img width="717" alt="Screenshot 2023-11-26 at 12 34 51 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/bcbc9344-7fcc-4c2c-98d6-05b35d428884">

<img width="454" alt="Screenshot 2023-11-26 at 12 30 07 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/ae5b6881-acbf-432d-8004-633250abcc9b">

We can see my EKS cluster has been created upon running the terraform file:

<img width="845" alt="Screenshot 2023-11-26 at 12 23 51 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/3677f373-14d8-4913-a3b5-2fc5547a0c88">

We can also see all of our EC2 instances running:

<img width="599" alt="Screenshot 2023-11-26 at 12 32 20 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/0b7f3773-d9c5-4337-bdfe-f88dddc2f208">

Now, we setup the aws cli on the build slave:

<img width="278" alt="Screenshot 2023-11-26 at 12 48 41 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/8e772ad5-68fc-4794-945e-080e394bc8a3">

We get the kubernetes credentials and now we can use `kubectl` commands:

<img width="573" alt="Screenshot 2023-11-26 at 12 49 55 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/ed02643c-46f2-4a22-a55f-4976f2e4a083">

Now we can add kubernetes manifest files to our project. Files that 1. create a namespace, 2. establish secret credentials with Jfrog, 3. deploy the pods, and 4. create a service to define this execution. 

<img width="262" alt="Screenshot 2023-11-26 at 11 32 28 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/89451da2-9904-4811-8a5f-2e873eaddf0e">

We do a test run on the build slave:

<img width="387" alt="Screenshot 2023-11-26 at 11 50 07 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/728b07e8-dd4b-4c64-8952-2c0c3f8ce41d">

<img width="569" alt="Screenshot 2023-11-26 at 11 53 13 AM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/d983131d-f35a-4925-a5c3-ebd8671c8641">

We can see its in "ImagePullBackOff" since it's failing to pull the JFrog image. I make a user called `dockercred` on JFrog, and then I use `docker login https://mfkimbell.jfrog.io` to login to my user on the build slave. We setup the `deply.sh` to run, and after running the `service.yaml` and open up port 3082 on the EKS containers, we can see our appliation running:

<img width="544" alt="Screenshot 2023-11-26 at 12 19 33 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/4bcdc2bb-dc60-496c-93d6-33ff048b8b62">

Now, we go to our Jenkinsfile and add a stage to automatically execute `deploy.sh`, which executes the kubernetes manifest files:

<img width="371" alt="Screenshot 2023-11-26 at 12 29 21 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/5ebee7d2-15cf-4c5d-baed-2d9870dbd85d">

After the commit we can see our job rerunning:

<img width="876" alt="Screenshot 2023-11-26 at 12 31 49 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/cc65dc73-a902-4a77-b7f9-a42c54656e25">

And again we see our applicatin up and running via Kubernetes pods:

<img width="469" alt="Screenshot 2023-11-26 at 12 32 46 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/8d2a138c-3335-4564-97c9-465684a4a0a7">

You live and you learn.... Don't leave EKS running...

<img width="819" alt="Screenshot 2023-11-26 at 12 49 27 PM" src="https://github.com/mfkimbell/terraform-aws-DevOps-pipeline/assets/107063397/1b41e3b6-9d3a-4065-8cc8-0d4c7108ccb7">


