# cloud4c-cicdb3

### Install jenkins in Linux env (Redhat|Centos|Oraclellinxu |amazon linux)

## steps 

### step 1 -- updating yum repo 

```
[ec2-user@ip-172-31-13-32 ~]$ sudo yum update
Failed to set locale, defaulting to C
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
amzn2-core                                                                                                            | 3.7 kB  00:00:00     
No packages marked for update
```

### step 2 -- adding software repo of jenkins 

```
 sudo wget -O /etc/yum.repos.d/jenkins.repo  https://pkg.jenkins.io/redhat-stable/jenkins.repo
```

### step 3 -- 

```
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum upgrade
```

### step 4 -- installing JDk 11 

```
sudo amazon-linux-extras install java-openjdk11 -y
```

### step 5 -- installing jenkins 

```
sudo yum install jenkins -y
```

### starting jenkins 

```
[ec2-user@ip-172-31-13-32 ~]$ sudo systemctl enable jenkins
Created symlink from /etc/systemd/system/multi-user.target.wants/jenkins.service to /usr/lib/systemd/system/jenkins.service.
[ec2-user@ip-172-31-13-32 ~]$ sudo systemctl start  jenkins
[ec2-user@ip-172-31-13-32 ~]$ sudo systemctl status  jenkins
● jenkins.service - Jenkins Continuous Integration Server
   Loaded: loaded (/usr/lib/systemd/system/jenkins.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2023-08-16 04:04:20 UTC; 35s ago
 Main PID: 4006 (java)

```

## to COnfigure CI or CD using jenkins there are various options we have

<img src="v1.png">

### job consist of 6 configuration steps 

<img src="jc1.png">

### a job also have given options after configuration 

<img src="jc2.png">

### A job build / execution result is having given options 

<img src="jc3.png">

### understanding user story 

<img src="user.png">

## Integration of jenkins 

<img src="ig.png">

### installing git support in the machine where jenkins is installed

```
[ec2-user@ip-172-31-13-32 ~]$ sudo yum install git -y 
Failed to set locale, defaulting to C
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
amzn2-core                                                                                                            | 3.7 kB  00:00:00     
Resolving Dependencies
--> Running transaction check
---> Package git.x86_64 0:2.40.1-1.amzn2.0.1 will be installed
--> Processing Dependency: perl-Git = 2.40.1-1.amzn2.0.1 for package: git-2.40.1-1.amzn2.0.1.x86_64
--> Processing Dependency: git-core-doc = 2.40.1-1.amzn2.0.1 for package: git-2.40.1-1.amzn2.0.1.x86_64
--> Processing Dependency: git-core = 2.40.1-1.amzn2.0.1 for package: git-2.40.1-1.amzn2.0.1.x86_64
--> Processing Dependency: perl(Term::ReadKey) for package: git-2.40.1-1.amzn2.0.1.x86_64
--> Processing Dependency: perl(Git) for package: git-2.40.1-1.amzn2.0.1.x86_64
--> Running transaction check
---> Package git-core.x86_64 0:2.40.1-1.amzn2.0.1 will be installed

```

### Creating account on Docker hub & github 

<img src="acc.png">




