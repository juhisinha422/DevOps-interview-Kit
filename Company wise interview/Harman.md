# Harman DevOps Interview Questions & Detailed Answers (4+ Years Experience)

---

# 1. Tell me about yourself

I am a DevOps Engineer with around 4 years of experience in cloud infrastructure, CI/CD automation, Kubernetes, Docker, Terraform, monitoring, and production support. In my current role, I mainly work on automating infrastructure provisioning, creating and maintaining CI/CD pipelines, managing Kubernetes clusters, troubleshooting production issues, and implementing monitoring and logging solutions.

I have hands-on experience with AWS services such as EC2, IAM, VPC, Load Balancers, Route53, CloudWatch, EKS, and S3. I also work with Infrastructure as Code using Terraform, where we manage cloud infrastructure through reusable modules and remote state management.

On the CI/CD side, I have worked extensively with Jenkins and GitHub Actions for automating build, test, and deployment workflows. I also have experience handling production incidents, preparing RCA reports, optimizing deployments, improving system reliability, and implementing security best practices.

---

# 2. Explain your project

In my current project, we are running microservices-based applications deployed on Kubernetes in AWS cloud. Applications are containerized using Docker and deployed into EKS clusters using Jenkins/GitHub Actions pipelines.

The architecture includes:

* AWS VPC with public/private subnets
* Application Load Balancer
* EKS cluster
* Docker containers
* Terraform-managed infrastructure
* Prometheus and Grafana monitoring
* Centralized logging using ELK/Loki
* CI/CD automation using Jenkins

Workflow:

```text id="0d5j7o"
Developer → Git Push → Jenkins Pipeline → Docker Build → Image Push → Kubernetes Deployment
```

My responsibilities include:

* Infrastructure provisioning
* Pipeline automation
* Monitoring and alerting
* Production troubleshooting
* Kubernetes administration
* Deployment automation
* Cost optimization
* Security hardening

---

# 3. How will you migrate Jenkins pipeline to GitHub Actions?

To migrate Jenkins pipelines to GitHub Actions, I first analyze the existing Jenkins pipeline stages such as:

* Checkout
* Build
* Unit testing
* Docker build
* Security scanning
* Deployment

Then I convert those stages into GitHub Actions workflow YAML format under:

```text id="tnc1w5"
.github/workflows/
```

Example workflow:

```yaml id="wmgf3h"
name: CI Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v4

    - name: Build
      run: mvn clean install

    - name: Docker Build
      run: docker build -t app .

    - name: Deploy
      run: kubectl apply -f deployment.yaml
```

Migration steps:

1. Analyze Jenkins stages
2. Convert Groovy logic to YAML
3. Configure GitHub secrets
4. Configure self-hosted runners if needed
5. Validate builds
6. Implement rollback strategy
7. Decommission Jenkins gradually

Advantages of GitHub Actions:

* Native GitHub integration
* Easier maintenance
* Better scalability
* Reduced infrastructure management

---

# 4. How do you design Idempotent automation scripts?

Idempotent scripts ensure that repeated execution produces the same result without causing issues or duplicate changes.

For example:

* Creating user only if not exists
* Installing package only if absent
* Updating config only if needed

Example Shell Script:

```bash id="cfjlwm"
if ! id "devops" &>/dev/null; then
    useradd devops
fi
```

Best practices for idempotent scripts:

* Check resource existence before creation
* Avoid duplicate operations
* Use declarative approaches
* Maintain desired state
* Validate before execution

In DevOps, idempotency is important because automation scripts may run multiple times through CI/CD or configuration management systems.

---

# 5. How will you do error handling and retries in your automation scripts?

Error handling is important to prevent automation failures and improve reliability.

In Shell Script:

```bash id="tv0m6h"
set -e
```

This stops script immediately if any command fails.

Retry example:

```bash id="9v3s4p"
for i in {1..3}
do
    curl https://example.com && break
    sleep 5
done
```

In Python:

```python id="5y2lnq"
try:
    result = api_call()
except Exception as e:
    print(e)
```

Best practices:

* Proper logging
* Exit codes
* Retry mechanisms
* Alerting
* Rollback handling
* Timeout configuration

---

# 6. Explain Xcode build failures

Xcode build failures generally occur because of:

* Dependency issues
* Incorrect certificates/provisioning profiles
* Version incompatibility
* Build signing errors
* Missing frameworks/libraries
* Cache corruption

Troubleshooting steps:

1. Check build logs
2. Verify certificates/profiles
3. Clean derived data
4. Validate dependency versions
5. Verify SDK compatibility
6. Rebuild project

Useful commands:

```bash id="e6w47u"
xcodebuild clean
```

and

```bash id="o0ruzh"
xcodebuild archive
```

In CI/CD pipelines, common failures happen due to:

* Expired certificates
* Incorrect environment variables
* Mac agent issues
* Dependency download failures

---

# 7. How do you make configurations for C++ projects?

For C++ projects, configuration management generally involves:

* Compiler configuration
* Dependency management
* Build flags
* Environment-specific configs

Tools commonly used:

* CMake
* Makefiles
* Conan/vcpkg

Example CMake:

```cmake id="jppghc"
cmake_minimum_required(VERSION 3.10)
project(App)

add_executable(app main.cpp)
```

In CI/CD:

* Build configurations automated
* Compiler versions standardized
* Dependency caching implemented
* Environment variables managed securely

---

# 8. What are the ways to encounter red build systems?

A "red build" means pipeline/build failure.

Common reasons:

* Compilation failure
* Unit test failure
* Dependency issue
* Environment issue
* Docker build failure
* Security scan failure
* Infrastructure issue
* Deployment failure

Troubleshooting approach:

1. Check failed stage
2. Analyze logs
3. Compare recent code changes
4. Verify environment/dependencies
5. Re-run build if intermittent
6. Rollback if needed

Best practices:

* Fail fast
* Proper notifications
* Build validation
* Automated testing

---

# 9. How do you debug builds in Gradle pipelines?

Debugging Gradle pipelines involves:

1. Checking build logs
2. Running Gradle in debug mode
3. Verifying dependency resolution
4. Checking plugin compatibility
5. Validating Java version

Useful commands:

```bash id="k09h22"
gradle build --stacktrace
```

```bash id="a4p15q"
gradle build --debug
```

```bash id="u2kt5q"
gradle dependencies
```

Common Gradle build issues:

* Dependency conflicts
* Java version mismatch
* Plugin incompatibility
* Memory issues
* Cache corruption

In CI/CD:

* Validate environment consistency
* Cache dependencies
* Use proper Gradle wrapper version

---

# 10. Write a script to implement a log parser

## a. Parse a log file line by line

## b. Count all error and warning occurrences in the log file

Example Python Script:

```python id="6iqpq9"
error_count = 0
warning_count = 0

with open("application.log", "r") as file:
    for line in file:
        line = line.lower()

        if "error" in line:
            error_count += 1

        if "warning" in line:
            warning_count += 1

print(f"Total Errors: {error_count}")
print(f"Total Warnings: {warning_count}")
```

Explanation:

* Opens log file
* Reads line by line
* Converts text to lowercase
* Checks for "error" and "warning"
* Maintains counters
* Prints summary

Production improvements:

* Regex-based parsing
* Timestamp filtering
* Multi-threaded parsing
* Alert generation
* Dashboard integration

Shell Script alternative:

```bash id="6oytyv"
grep -i "error" application.log | wc -l
grep -i "warning" application.log | wc -l
```

This counts total error and warning occurrences from logs.

---
