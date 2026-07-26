1. First do the "Build Stage", Test-Stage, SonarQube, Nexus-Stage, Tomcat-Deploy, Docker-Push and Deploy to EKS

2. While Integrate with EKS to Runner be careful.

3. In Runner Server Install aws commands, eksctl, kubectl, aws configure, access_key and pass_key.

4. And later Add to Runner for ROLE_EKSCTL_JULY_26 like this name, Goto Instances, Security, Modify Role, and Select ROLE_EKSCTL_JULY_26.

5. Kubernetes yaml please keep in github or anywhere but give path like kubernetes apply -f kubernetes/path/deployment.yaml.


Based on everything we've worked through together, here's a refined checklist that includes the important steps and the mistakes to avoid.

# Complete CI/CD Pipeline (Maven → SonarQube → Nexus → Tomcat → Docker → EKS)

## Pipeline Stages

1. Build Stage
2. Test Stage
3. SonarQube Stage
4. Nexus Stage
5. Tomcat Deploy Stage
6. Docker Push Stage
7. Deploy to EKS Stage

---

# 1. Build Stage

* Checkout the code.
* Install Java (JDK 17/21).
* Install Maven.
* Build the application.

```bash
mvn clean package
```

Artifact:

```
target/*.war
```

---

# 2. Test Stage

Run the unit tests.

```bash
mvn test
```

---

# 3. SonarQube Stage

Configure:

* SONAR_HOST_URL
* SONAR_TOKEN

Run:

```bash
mvn sonar:sonar
```

Verify the Quality Gate before continuing.

---

# 4. Nexus Stage

Upload the generated WAR file.

```bash
mvn deploy
```

or upload manually using:

```bash
curl
```

Store:

```
target/*.war
```

inside Nexus Repository.

---

# 5. Tomcat Deploy Stage

Download the WAR from Nexus.

Copy it into Tomcat.

```
webapps/
```

Restart Tomcat.

Verify:

```
http://<Tomcat-IP>:8080/application-name
```

---

# 6. Docker Push Stage

Build Docker image.

Example:

```bash
docker build -t anildocker11/maven-webapp:${GITHUB_SHA} .
```

Login.

```bash
docker login
```

Push image.

```bash
docker push anildocker11/maven-webapp:${GITHUB_SHA}
```

---

# 7. Deploy to EKS

Configure AWS credentials.

```bash
aws configure
```

Update kubeconfig.

```bash
aws eks update-kubeconfig \
--region ap-south-1 \
--name my-k8s-cluster
```

Deploy.

```bash
kubectl apply -f kubernetes/deployment.yaml
```

Check rollout.

```bash
kubectl rollout status deployment/javawebappdep
```

---

# Runner Server Configuration

Install AWS CLI

```bash
sudo apt install unzip

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip

unzip awscliv2.zip

sudo ./aws/install
```

---

Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl

sudo mv kubectl /usr/local/bin/
```

Verify

```bash
kubectl version --client
```

---

Install eksctl

```bash
curl --silent --location \
"https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" \
| tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin
```

Verify

```bash
eksctl version
```

---

Configure AWS

```bash
aws configure
```

Provide

```
Access Key
Secret Key
Region
Output Format
```

Verify

```bash
aws sts get-caller-identity
```

---

# Attach IAM Role to Runner EC2

Go to

```
EC2
↓

Instances
↓

Select Runner Instance
↓

Actions

↓

Security

↓

Modify IAM Role
```

Select

```
ROLE_EKSCTL_JULY_26
```

Save.

Verify

```bash
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

---

# Kubernetes YAML

Store manifests inside GitHub.

Example:

```
Repository
│
├── .github
│   └── workflows
│
├── kubernetes
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── namespace.yaml
│   ├── ingress.yaml
│   └── configmap.yaml
```

Deploy

```bash
kubectl apply -f kubernetes/deployment.yaml

kubectl apply -f kubernetes/service.yaml

kubectl apply -f kubernetes/ingress.yaml
```

---

# Mistakes to Avoid (Based on Your Troubleshooting)

### GitHub Actions

* ❌ Used `ubuntu:latest` instead of `ubuntu-latest`.
* ❌ Misspelled `actions/download-artifact`.
* ❌ Forgot `actions/checkout@v4`.
* ❌ Used incorrect file paths for Kubernetes manifests.

---

### Kubernetes YAML

* ❌ `apiVersion: appa/v1` instead of `apps/v1`.
* ❌ Used `matchLabels` directly under `spec` instead of under `selector`.
* ❌ Used `port:` instead of `ports:` for the container.
* ❌ Used an invalid container name containing `/`.
* ❌ Set `targetPort` to the NodePort instead of the container port.
* ❌ Used `Kubernetes/` (uppercase `K`) instead of `kubernetes/` in the workflow path on Linux.

---

### AWS / EKS

* ❌ Used the Availability Zone (`ap-south-1b`) instead of the Region (`ap-south-1`) with `aws eks update-kubeconfig`.
* ❌ Cluster name case mismatch (for example, `my-K8s-cluster` vs. `my-k8s-cluster`).
* ❌ Missing AWS credentials on the runner.
* ❌ EC2 instance did not initially have the required IAM role attached.
* ❌ Root account was not authorized to access the EKS cluster.
* ❌ Security Groups did not allow the required NodePort/LoadBalancer traffic during testing.

---

### Docker

* ❌ Docker image tag mismatch between the build and deployment.
* ❌ WAR file not found because the build artifact path was incorrect.

---

### GitHub Secrets

Ensure these are configured:

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`
* `AWS_REGION`
* `EKS_CLUSTER_NAME`
* `DOCKER_REPOSITORY`
* `DOCKER_USERNAME`
* `DOCKER_PASSWORD`
* `SONAR_TOKEN`
* `NEXUS_USERNAME`
* `NEXUS_PASSWORD`

This checklist reflects both the intended CI/CD workflow and the issues you encountered while setting up GitHub Actions, Docker, Tomcat, Nexus, and EKS.
