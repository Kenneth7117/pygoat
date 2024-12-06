# DevSecOps CI/CD Pipeline with Pygoat  
This repository demonstrates a **DevSecOps CI/CD pipeline** using the vulnerable application [Pygoat](https://github.com/OWASP/PyGoat). The project integrates **Bandit** for Static Application Security Testing (SAST) and **Docker Scout** for Docker image scanning, leveraging **GitHub Actions** for the CI/CD workflow.  

## Key Features  
- **Pygoat**: An OWASP project designed to teach and identify vulnerabilities in Python web applications.  
- **SAST with Bandit**: Automated analysis of Python code to identify potential security issues.  
- **Container Scanning with Docker Scout**: Analysis of Docker images to detect vulnerabilities in dependencies and configurations.  
- **CI/CD with GitHub Actions**: A fully automated workflow to ensure security scans are integrated into the development process.  

## Workflow Overview  
The GitHub Actions pipeline is triggered on:  
- Pushes to the `master` branch.  
- Pull request events targeting the `master` branch.  

### Pipeline Steps:  
1. **Code Checkout**: The repository is checked out to the runner.  
2. **Bandit Scan**: Static analysis of Python code using Bandit.  
3. **Docker Build**: Builds a Docker image for the application.  
4. **Docker Scout Scan**: Scans the built Docker image for vulnerabilities.  
5. **Test and Deploy**: Optional steps for automated testing and deployment.  

## GitHub Actions Workflow  

### Run Workflow Condition

```
on: [push]
```

- The CI workflow is inititated on the push of commit to the repository

### SAST Scan

```
jobs:
 sast_scan:
   name: Run Bandit Scan
   runs-on: ubuntu-latest

   steps:
   - name: Checkout code
     uses: actions/checkout@v2

   - name: Set up Python
     uses: actions/setup-python@v2
     with:
       python-version: 3.8

   - name: Install Bandit
     run: pip install bandit

   - name: Run Bandit Scan
     run: bandit -ll -ii -r . -f json -o bandit-report.json

   - name: Upload Artifact
     uses: actions/upload-artifact@v3
     if: always()
     with:
      name: bandit-findings
      path: banditken-report.json
```

- This is the first job of the CI/CD pipeline. It is made to run on the latest Ubuntu OS hosted by Github. The actions/checkout is the code used to check out the repo. Information on it can befound in their official Github repo, @v2 is the version 2 of that repo's action code.

- actions/setup-python@v2 is used to setup the python interpreter on the environment with the desired version.

- run: pip install bandit is to install bandit. not this is not an action unlike the other, the run syntax runs command as it is on the cli

- run: bandit -ll -ii -r . -f json -o bandit-report.json. This runs the bandit scan with medium and high vulenearbilities. This also exports the data as a json file

- uses: actions/upload-artifact@v3. This commands are to upload the outputted data in as aritfact for local download to the machine.

### Docker Image Scan

```

image_scan:
   name: Build Image and Run Image Scan
   runs-on: ubuntu-latest

   steps:     
   - name: Checkout code
     uses: actions/checkout@v2

   - name: Set up Docker
     uses: docker-practice/actions-setup-docker@v1
     with:
      docker_version: '20.10.7'

   - name: Build Docker Image
     run: docker build -f Dockerfile -t myapp:latest .

   - name: Docker Scout Scan
     uses: docker/scout-action@v1.15.1
     with:
      dockerhub-user: ${{secrets.REPO_USER}}
      dockerhub-password: ${{secrets.REPO_PWD}}
      command: quickview,cves
      only-severities: critical,high
      sarif-file: scoutken-report.sarif
      exit-code: true

   - name: Upload Artifact
     uses: actions/upload-artifact@v3
     if: always()
     with:
      name: docker-scout-findings
      path: scoutken-report.sarif

```

- Github runs its jobs in different machines, hence the action/checkout command is required to re-innitiated.

- docker-practice/actions-setup-docker@v1 is a command from the official repo to install docker with the desired version.

- docker build -f Dockerfile -t myapp:latest . to build the docker image from the listed docker file and naming it myapp with the version as the latest

- docker/scout-action@v1.15.1 is again an official docker repo action. This is used to initiate the image scan. We can see this requiring the docker login, which is provided using the environment variable and github secrets manager.

- The severity and the output file is set as required and agin artifact is used to get the data for local download to the machine.
