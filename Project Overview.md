Project Overview
Objective: Update the graphics (Landing page) of a 3-tier application deployed in a Kubernetes cluster on AWS, ensuring minimal downtime and maintaining service availability for 600 clients.

Duration: 4 months

Team Composition:

Developers: 4 developers focused on front-end (UI/UX) and back-end logic.
DevOps Engineers: 2 DevOps engineers (including yourself) responsible for the infrastructure, CI/CD, deployment, monitoring, and troubleshooting.
Application Architecture
Application Type: 3-tier architecture

Presentation Layer: The landing page and UI components, using technologies like React, Angular, or similar.
Application Layer: Backend services, possibly using Spring Boot, Node.js, or similar frameworks.
Data Layer: Relational database (e.g., MySQL, PostgreSQL) for storing application data.
Clients: 600 concurrent users, with peak usage during weekdays.

Infrastructure Details
Cloud Provider: AWS

Server Type:

EC2 Instances: Utilized for hosting Kubernetes master and worker nodes. Instances are optimized based on the application's resource requirements.
Kubernetes Cluster:

Cluster Type: Self-managed Kubernetes cluster on AWS.
Cluster Management:
Managed using tools like kubeadm for initial setup and kubectl for ongoing operations.
Helm is used for managing Kubernetes applications and deploying Helm charts.
Master Nodes:

Quantity: 1 master node.
Instance Type: t3.large (2 vCPUs, 8GB RAM) to handle control plane operations.
Worker Nodes:

Quantity: 3 worker nodes.
Instance Type: t3.medium (2 vCPUs, 4GB RAM) each.
Scaling: Auto-scaling is configured with Cluster Autoscaler to dynamically adjust the number of worker nodes based on demand.
Networking:

VPC: Custom VPC with public and private subnets for security and network isolation.
Load Balancer: AWS Elastic Load Balancer (ELB) distributes traffic to the worker nodes.
Ingress Controller: NGINX Ingress Controller handles routing of external traffic to the appropriate services within the cluster.
Storage:

Persistent Volumes (PVs): AWS EBS volumes used for data persistence, especially for the database pods.
Storage Classes: Defined to automate the provisioning of persistent storage.
Pod Details
Total Pods in Cluster: 12 Pods

Pods per Node:
Each worker node can run up to 4 pods based on resource allocation and usage.
Therefore, the total capacity for pods across the 3 worker nodes is approximately 12 pods.
DevOps Process
Code Management:

Version Control: GitHub is used for code management.
Branching Strategy:
Feature Branches: Developers create branches for specific features or bug fixes.
Pull Requests: Code changes are reviewed via pull requests before being merged into the main branch.
CI/CD Pipeline:

Tool: Jenkins is the primary tool for managing the CI/CD pipeline.
Integration: Jenkinsfile stored in the repository defines the pipeline stages.
CI/CD Pipeline Stages
1. Code Checkout
Purpose: Retrieve the latest code from the GitHub repository.
Commands:
groovy
Copy code
stage('Checkout') {
    steps {
        git branch: 'develop', url: 'https://github.com/jaiswaladi2468/Shopping-Cart.git'
    }
}
2. Build Stage
Purpose: Build the application using Maven and run unit tests.
Commands:
groovy
Copy code
stage('Build') {
    steps {
        sh 'mvn clean install'
    }
}
3. SonarQube Analysis
Purpose: Perform static code analysis using SonarQube.
Commands:
groovy
Copy code
stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('SonarQube') {
            sh 'mvn sonar:sonar'
        }
    }
}
4. Docker Build
Purpose: Build Docker images for the application services.
Commands:
groovy
Copy code
stage('Docker Build') {
    steps {
        script {
            def services = ['serviceA', 'serviceB', 'serviceC']
            for (service in services) {
                sh "docker build -t myregistry/${service}:${env.BUILD_NUMBER} ./docker/${service}"
            }
        }
    }
}
5. Push to Docker Registry
Purpose: Push the built Docker images to the Docker registry.
Commands:
groovy
Copy code
stage('Push to Registry') {
    steps {
        script {
            def services = ['serviceA', 'serviceB', 'serviceC']
            for (service in services) {
                sh "docker push myregistry/${service}:${env.BUILD_NUMBER}"
            }
        }
    }
}
6. Deploy to Kubernetes
Purpose: Deploy the updated Docker images to the Kubernetes cluster.
Commands:
groovy
Copy code
stage('Deploy to Kubernetes') {
    steps {
        script {
            sh 'kubectl set image deployment/serviceA serviceA=myregistry/serviceA:${env.BUILD_NUMBER}'
            sh 'kubectl set image deployment/serviceB serviceB=myregistry/serviceB:${env.BUILD_NUMBER}'
            sh 'kubectl set image deployment/serviceC serviceC=myregistry/serviceC:${env.BUILD_NUMBER}'
        }
    }
}
7. Test Deployment
Purpose: Run smoke tests to validate the deployment.
Commands:
groovy
Copy code
stage('Test Deployment') {
    steps {
        sh 'curl -f http://your-app-url/health || exit 1'
    }
}
8. Notifications
Purpose: Notify the team of the build and deployment status.
Commands:
groovy
Copy code
stage('Notifications') {
    steps {
        script {
            if (currentBuild.result == 'SUCCESS') {
                sh 'curl -X POST -H "Content-Type: application/json" -d \'{"text": "Build Successful!"}\' https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK'
            } else {
                sh 'curl -X POST -H "Content-Type: application/json" -d \'{"text": "Build Failed!"}\' https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK'
            }
        }
    }
}
Deployment Strategy
Environment:

The entire update was first tested in the development environment to ensure that all changes were stable and did not impact the existing application.
Strategy:

Blue/Green Deployment: A blue/green deployment strategy was considered for production, allowing the team to switch between two environments (blue and green) with minimal downtime.
Downtime:

Scheduled Maintenance: Updates were applied during scheduled weekend downtimes, minimizing disruption to the 600 clients.
Post-Deployment:

Monitoring: Application performance and cluster health were closely monitored using Prometheus and Grafana dashboards.
Log Management: ELK Stack (Elasticsearch, Logstash, Kibana) was used for log aggregation and analysis, aiding in troubleshooting any post-deployment issues.
Your Roles and Responsibilities
Infrastructure Setup:

Provisioned EC2 instances and configured them to form a Kubernetes cluster.
Installed and configured Kubernetes components (kubeadm, kubectl, kubelet) on master and worker nodes.
Set up networking, security groups, and load balancers to ensure proper traffic routing and security.
CI/CD Pipeline Management:

Developed and maintained Jenkins pipeline scripts, ensuring smooth and automated builds and deployments.
Integrated SonarQube for code quality checks and ensured quality gates were met before deployment.
Managed Docker images, including building, scanning, and pushing to the registry.
Deployment Process:

Coordinated with developers to update the Kubernetes manifests and Helm charts with new image versions.
Executed deployments using a rolling update strategy, ensuring zero downtime for the users.
Validated deployments through automated tests and manual checks.
Monitoring and Troubleshooting:

Implemented Prometheus and Grafana for real-time monitoring of the application and cluster health.
Set up alerting rules to notify the team of any issues, such as high CPU usage or pod failures.
Used the ELK Stack to aggregate logs from all pods and nodes, making it easier to identify and resolve issues.
Collaboration and Documentation:

Collaborated with the development team to ensure that the infrastructure supported the new changes.
Documented the entire CI/CD process, deployment strategies, and infrastructure setup, providing a clear reference for future work.
Conducted knowledge-sharing sessions with other team members to discuss best practices and lessons learned.
Continuous Improvement:

Identified and implemented improvements to the CI/CD pipeline, such as reducing build times or automating additional steps.
Explored new tools and technologies to enhance the DevOps processes and stay updated with industry trends.
Daily Routine for the CI/CD Pipeline
Monitor Jenkins: Check the Jenkins dashboard for build status and initiate manual builds if necessary.
Review Pipeline Logs: Examine the logs for any failed builds or tests and troubleshoot as needed.
Coordinate with Developers: Communicate any issues found during builds or tests and discuss necessary code changes.
Update Documentation: Maintain documentation related to the CI/CD processes and any changes made.
Run Regular Maintenance: Periodically review and optimize the Kubernetes cluster for performance, resource usage, and scaling needs.