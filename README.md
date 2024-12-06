# DevSecOps CI/CD Pipeline with Pygoat  
This repository demonstrates a **DevSecOps CI/CD pipeline** using the vulnerable application [Pygoat](https://github.com/OWASP/PyGoat). The project integrates **Bandit** for Static Application Security Testing (SAST) and **Docker Scout** for Docker image scanning, leveraging **GitHub Actions** for the CI/CD workflow.  

## Key Features  
- **Pygoat**: An OWASP project designed to teach and identify vulnerabilities in Python web applications.  
- **SAST with Bandit**: Automated analysis of Python code to identify potential security issues.  
- **Container Scanning with Docker Scout**: Analysis of Docker images to detect vulnerabilities in dependencies and configurations.  
- **CI/CD with GitHub Actions**: A fully automated workflow to ensure security scans are integrated into the development process.  

## Workflow Overview  
The GitHub Actions pipeline is triggered on:  
- Pushes to the `main` branch.  
- Pull request events targeting the `main` branch.  

### Pipeline Steps:  
1. **Code Checkout**: The repository is checked out to the runner.  
2. **Bandit Scan**: Static analysis of Python code using Bandit.  
3. **Docker Build**: Builds a Docker image for the application.  
4. **Docker Scout Scan**: Scans the built Docker image for vulnerabilities.  
5. **Test and Deploy**: Optional steps for automated testing and deployment.  

## Getting Started  

### Prerequisites  
- A GitHub account with access to this repository.  
- Docker installed locally for running manual scans.  

### Running Locally  
1. Clone this repository:  
   ```bash  
   git clone https://github.com/your-username/pygoat-devsecops.git  
   cd pygoat-devsecops  
