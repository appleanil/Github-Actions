SELF-HOSTED
===========
1. EC2 Instance t2.medium with 30GB with GithubActions.
1. Got settings ---> Actions ---> Runners.
2. Self hosted runner ---> Linux and run those commands at last ./run.sh then only Github Actions connect
3. In Mobxsterm better to keep EKS, Docker and Runner separtely

4. In Docker install sudo apt docker.io -y
5. sudo usermod -aG docker ubuntu
5. docker login -u anildocker11 and give password Applejeans@630
6. Keep dockerfile in same context name is dockerfile
7. FROM tomcat:9.0.100
   COPY target/maven-web-application.war /usr/local/tomcat/webapps/maven-web-application.war
6. Once the code is trigger upto docker then go and check in docker server automatically came up our image name
6. Exit and reconnect and check docker images and docker ps

7. Install the aws commands and unzip it and check once aws --version
8. Integrate the aws configure, access key and security key
9. aws eks update-kubeconfig --region <region> --name <cluster-name>
10. In deployment yaml file take our image and update it
7. If We want connect the SonarQube server or else leave it
8. docker run -d --name sonarqube -p 9000:9000 sonarqube:community
9. check docker ps and docker images
10. Integrate to sonarqube details in pom.xml file.
11. Once trigger the actions then go and check in cd/runnner there is a folder name is called maven-web-application.
12. Add code for Build, SonarQube, Security Checks, Nexus, Tomcat and Kubernetes.





1. EC2 Instance: **t2.medium** with **30 GB** storage for GitHub Actions.

2. Go to **Settings → Actions → Runners**.

3. Select **Self-hosted Runner → Linux**, copy and run all the commands. Finally, run `./run.sh`. Only then will GitHub Actions connect to the runner.

4. In **MobaXterm**, it is better to keep **EKS**, **Docker**, and the **GitHub Actions Runner** on separate EC2 instances.

5. Install Docker:

```bash
sudo apt install docker.io -y
```

6. Add the `ubuntu` user to the Docker group:

```bash
sudo usermod -aG docker ubuntu
```

7. Log in to Docker Hub:

```bash
docker login -u anildocker11
```

Enter the Docker Hub password when prompted.

8. Keep the **Dockerfile** in the project's root directory.

9. Dockerfile:

```dockerfile
FROM tomcat:9.0.100

COPY target/maven-web-application.war /usr/local/tomcat/webapps/maven-web-application.war
```

10. Once the GitHub Actions workflow reaches the Docker stage, verify that the Docker image has been created on the Docker server.

11. Exit and reconnect to the server, then verify Docker using:

```bash
docker images

docker ps
```

12. Install the AWS CLI, unzip it (if required), and verify the installation:

```bash
aws --version
```

13. Configure AWS CLI using your **Access Key** and **Secret Access Key**:

```bash
aws configure
```

14. Connect to the EKS cluster:

```bash
aws eks update-kubeconfig --region <region> --name <cluster-name>
```

15. Update the Docker image in the Kubernetes `deployment.yaml` file.

16. If you want to integrate SonarQube, run the SonarQube Community Edition container:

```bash
docker run -d --name sonarqube -p 9000:9000 sonarqube:community
```

17. Verify the SonarQube container:

```bash
docker ps

docker images
```

18. Integrate the SonarQube configuration into the `pom.xml` file.

19. Once the GitHub Actions workflow is triggered, navigate to the runner directory and verify that the `maven-web-application` folder has been created:

```bash
cd actions-runner/_work/
```

20. Add the following stages to your GitHub Actions workflow:

* Build
* SonarQube
* Security Checks
* Nexus
* Tomcat
* Kubernetes
