# Docker-Jenkins-Git Project #2
HEY GUYS!!
This is my second mini-project(task) which is based on integrating the tools like jenkins, github, docker. This was one of the tasks which I love the most as it highlighted the industry use case which I took approx. 12 hrs to complete.


##Problem Statement :
1. Create a container image that’s has Jenkins installed using Dockerfile. 

2. When we launch this image, it should automatically start the Jenkins service in the container.

3. Create a job chain of job1, job2, job3 and job4 using the build pipeline plugin in Jenkins.

4. Job1: Pull the GitHub repo automatically when some developers push the repo to GitHub.

5. Job2: By looking at the code or program file, Jenkins should automatically start the respective language interpreter install image container to deploy code ( eg. If code is of PHP, then Jenkins should start the container that has PHP already installed ).

6. Job3: Test your app if it is working or not.

7. Job4: If the app is not working, then send an email to the developer with error messages.

8. Create One extra job job5 for monitor: If the container where the app is running. fails due to any reason then this job should automatically start the container again.


##Solution

### Step 1:
   #### Creation of Dockerfile of jenkins(not the pre-configured jenkins) of first installing it with few necessary other tools.
First made the workspace `task2` where Dockerfile was created. Inside Dockerfile installation of jenkins, python3, git, some tweaks for jenkins to run smoothly.
**FROM:** the image I used here was centos 8(latest)

**RUN:** the commands to be executed while building the image whcih will all be present in all the containers by default which uses this image for creation.
	
As some features are not installed by default in various images with many softwares not configured in yum repolist.
  * installing wget to download files from the url
  * setting up yum repository for jenkins from the url
  * install jenkins
  * installing java-11-openjdk as jenkins requires java as base.
  * installing python36 for execution of mail.py(python) file.
  * installing git
  
**COPY:** here I copied the smtp mail code from the local host to the image permanently, the file used was `mail.py`
**CMD:**  the cmd used here will start Jenkins on running of docker container and keep Jenkins live till the container is active.
Below is the image.

![01](Screenshots/T1.png)
![abc](Screenshots/Mail.png)

Building of image cmd used was 
	`docker build -t jenkinsos1:v4`
While running a container command used was
	`docker run -it --name os1 -p 8082:8080 --privileged -v /:/host jenkinsos:v4`
Here privileged was used so that docker can easily use command like chroot and access the root directory of the VM it is running on. It provides special privileges.

![02](Screenshots/T2.png)
![03](Screenshots/T3.png)

### Step 2:
   #### Setting up jenkins inside the container with the initialpassword provided.(USER PASSWORD CAN BE CHANGED-RECOMMENDED)
This is one-time for a particular container until its not removed from the localhost. This and the next whole setup which I did will stay safe until the container is removed completely. I am not showing this how it is done. PS: You can google it ;D

### Step 3:
   #### Coming onto the actual DevOps part where jobs are created which is explained below.
   
# Job Configuration

## Job 1: 
**Q) Pull the GitHub repo automatically when some developers push the repo to GitHub.**
A) So basically what is done here is that the files are copied from github repository to the localhost in some local repository.
Configuration image below.

![04](Screenshots/T4.png)

## Job 2:
**Q) By looking at the code or program file, Jenkins should automatically start the respective language interpreter install image container to deploy code ( eg. If code is of PHP, then Jenkins should start the container that has PHP already installed ).**
A) Basically what I did here that I used basic linux commands like `ls, grep, |` to decide from the file extension and launch containers accordingly for deploying it over the container with alloting specific port no. Similarly other extensions can be tried, with pulling a docker image too at the runtime.
Configuration image below.

![05](Screenshots/T5.png)

After execution of Job2 one can easily see whether the images was created or not.

![06](Screenshots/job2.png)

## Job 3:
**Q) Test your app if it is working or not.**
**A)** What I did here was I will check for the status of the running website. If it does not give 200 as the http_code then build fails or if it succeeds then exit with code 1.
Configuration image below.

![07](Screenshots/T6.png)

**Note-** The post build action takes place depending upon Job 3 success or failure.

## Job 4:
**Q) If the app is not working, then send an email to the developer with error messages.**
A) So the pre configured `mail.py` is run with python3 and sends Test Status message only if Job 3 failed otherwise it won't execute.
Configuration image below.

![08](Screenshots/T7.png)

## Job 5:
**Q) If the container where the app is running fails due to any reason then this job should automatically start the container again.**
A) For this monitoring job, I set a condition command to grep the running container if it doesn't find it then it will create the image. So to do that I used build periodically trigger for every minute to check the status of the container.
Configuration image below.

![09](Screenshots/T8.png)
Different conditions can be set for variety possible failures of Job 1, Job 2, Job 3. Container will be created accordingly.
![10](Screenshots/T12.png)


# Build Pipeline

So there are two possible cases happening here if the setup is done perfectly.

1) Job 1, Job 2, Job 3 runs and there is no need for Job 4 to be run. Job 5 shows that there is no fault with the container creation.

![11](Screenshots/T9.png)
![12](Screenshots/T10.png)

2) Job 1, Job 2 runs and Job 3 fails then Job 4 will execute and the mail will be sent.

![13](Screenshots/T11.png)
![14](Screenshots/T13.png)
