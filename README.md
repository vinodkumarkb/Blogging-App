BLOGGING APP

TOOLS:
AWS : Creating virtual machines .
Jenkins for automating the build, test, and deployment processes.
SonarQube for static code analysis to ensure code quality.
Trivy file scan to scan files and vulnerability scanning for Docker images.
Nexus Repository Manager for managing artifacts. Terraform as infrastructure as code to create EKS Cluster. Docker: Containerization for consistency and portability. Kubernetes: Container orchestration for deployment.
Prometheus and Grafana for monitoring the pipeline and application performance.

Step 1 : Set Up GitHub Repository and Push Local Code


Create a New GitHub Repository:


1.Login to GitHub:
-Visit GitHub and log in to your account.
2.Create a New Repository:
-Click on the "+" icon in the top-right corner and select "New repository."
-Repository Name: Enter a name for your repository.
-Description: (Optional) Add a brief description of your project.
-Visibility: Choose between Public or Private.
-Initialize Repository:
3.Click on "Create repository.”


2.2 Push Existing Local Code to GitHub:


1.Initialize Git in Your Local Project:
-Open your terminal or command prompt.
-Navigate to your project directory:
cd /path/to/your/project
-Initialize Git:
git init


2.Add Remote Repository:
-Add your GitHub repository as a remote:


git remote add origin https://github.com/vinodkumarkb/full-Stack-Blogging-App.git
-Replace `your-username` and `Blogging-App` with your GitHub username and repository name.


3.Add and Commit Your Code:
-Stage all changes:


git add .
-Commit the changes:
git commit -m "Initial commit"


4.Push to GitHub:
-Push your code to the `main` branch:
git push -u origin main


Repository Link
Repository URL Add your GitHub repository link here: https://github.com/vinodkumarkb/Blogging-App.git

Step 2. Launch Virtual Machine for Jenkins, Sonarqube and Nexus
Here is a detailed list of the basic requirements and setup for the EC2 instance i have used for running Jenkins, including the specifics of the instance type, AMI, and security groups.
EC2 Instance Requirements and Setup:
1.Instance Type
-Instance Type: `t2.large`
-vCPUs: 2
-Memory: 8 GB
-Network Performance: Moderate
2.Amazon Machine Image (AMI)
-AMI: Ubuntu Server 20.04 LTS (Focal Fossa)
3.Security Groups
Security groups act as a virtual firewall for your instance to control inbound and outbound traffic.



After Launching your Virtual machine ,SSH into the Server.
Step3. Installing Jenkins on Ubuntu

Execute these commands on Jenkins Server #!/bin/bash
# Install OpenJDK 17 JRE Headless
sudo apt install openjdk-17-jre-headless -y # Download Jenkins GPG key
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \ https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key # Add Jenkins repository to package manager sources
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \ https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null # Update package manager repositories sudo apt-get update
# Install Jenkins
sudo apt-get install jenkins -y

Save this script in a file, for example, install_jenkins.sh, and make it executable using:
chmod +x install_jenkins.sh

Then, you can run the script using:
./install_jenkins.sh

This script will automate the installation process of OpenJDK 17 JRE Headless and Jenkins.

Install docker 

Execute these commands on Jenkins, SonarQube and Nexus Servers

#!/bin/bash
# Update package manager repositories sudo apt-get update
# Install necessary dependencies
sudo apt-get install -y ca-certificates curl # Create directory for Docker GPG key sudo install -m 0755 -d /etc/apt/keyrings # Download Docker's GPG key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o
/etc/apt/keyrings/docker.asc
# Ensure proper permissions for the key sudo chmod a+r /etc/apt/keyrings/docker.asc # Add Docker repository to Apt sources
echo "deb [arch=$(dpkg --print-architecture) signed by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu
\
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \ sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
# Update package manager repositories sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx- plugin
docker-compose-plugin
Save this script in a file, for example, install_docker.sh, and make it executable using:
chmod +x install_docker.sh

Then, you can run the script using:
./install_docker.sh

 SetUp Nexus

Execute these commands on Nexues VM
#!/bin/bash
# Update package manager repositories sudo apt-get update
# Install necessary dependencies
sudo apt-get install -y ca-certificates curl # Create directory for Docker GPG key sudo install -m 0755 -d /etc/apt/keyrings # Download Docker's GPG key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o
/etc/apt/keyrings/docker.asc
# Ensure proper permissions for the key sudo chmod a+r /etc/apt/keyrings/docker.asc # Add Docker repository to Apt sources
echo "deb [arch=$(dpkg --print-architecture) signed by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu
\
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \ sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
# Update package manager repositories sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx- plugin
docker-compose-plugin

Save this script in a file, for example, install_docker.sh, and make it executable using:
chmod +x install_docker.sh

Then, you can run the script using:
./install_docker.sh

Create Nexus using docker container
To create a Docker container running Nexus 3 and exposing it on port 8081, you can
use the following command:
docker run -d --name nexus -p 8081:8081 sonatype/nexus3:latest This command does the following:
•-d: Detaches the container and runs it in the background.
•--name nexus: Specifies the name of the container as "nexus".
•-p 8081:8081: Maps port 8081 on the host to port 8081 on the container, allowing
access to Nexus through port 8081.
•sonatype/nexus3:latest: Specifies the Docker image to use for the container,
in this
case, the latest version of Nexus 3 from the Sonatype repository.
After running this command, Nexus will be accessible on your host machine at http://IP:8081.
Get Nexus initial password
Your provided commands are correct for accessing the Nexus password stored in the
container. Here's a breakdown of the steps:
1.Get Container ID: You need to find out the ID of the Nexus container. You can do this by running:
docker ps
This command lists all running containers along with their IDs, among other information.
2.Access Container's Bash Shell: Once you have the container ID, you can execute the docker exec command to access the container's bash shell: docker exec -it <container_ID> /bin/bash
Replace <container_ID> with the actual ID of the Nexus container.
3.Navigate to Nexus Directory: Inside the container's bash shell, navigate to the directory where Nexus stores its configuration:
cd sonatype-work/nexus3
4.View Admin Password: Finally, you can view the admin password by displaying the contents of the admin.password file:
cat admin.password
5.Exit the Container Shell: Once you have retrieved the password, you can exit the container's bash shell:
exit
This process allows you to access the Nexus admin password stored within the container.

SetUp SonarQube

Execute these commands on SonarQube VM
#!/bin/bash
# Update package manager repositories sudo apt-get update
# Install necessary dependencies
sudo apt-get install -y ca-certificates curl # Create directory for Docker GPG key sudo install -m 0755 -d /etc/apt/keyrings # Download Docker's GPG key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o
/etc/apt/keyrings/docker.asc
# Ensure proper permissions for the key sudo chmod a+r /etc/apt/keyrings/docker.asc # Add Docker repository to Apt sources
echo "deb [arch=$(dpkg --print-architecture) signed by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu
\
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \ sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
# Update package manager repositories sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx- plugin
docker-compose-plugin
Save this script in a file, for example, install_docker.sh, and make it executable using:
chmod +x install_docker.sh


Then, you can run the script using:
./install_docker.sh

Create Sonarqube Docker container
To run SonarQube in a Docker container with the provided command, you can follow
these steps:
1.Open your terminal or command prompt.
2.Run the following command:
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
This command will download the sonarqube:lts-community Docker image from Docker
Hub if it's not already available locally. Then, it will create a container named "sonar"
from this image, running it in detached mode (-d flag) and mapping port 9000 on the
host machine to port 9000 in the container (-p 9000:9000 flag).
3.Access SonarQube by opening a web browser and navigating to http://VmIP:9000.
This will start the SonarQube server, and you should be able to access it using the
provided URL. If you're running Docker on a remote server or a different port, replace localhost with the appropriate hostname or IP address and adjust the port
accordingly.

Step 4 .Install and Configur Plugins on Jenkins

Plugins:
1.SonarQube Scanner
2.Config file provider
3.Maven Integration
4.Pipeline maven integration.
5.Kubernetes
6.Kubernetes Client API
7.Kubernetes Credentials
8.Kubernetes CLI
9.Docker
10.Docker Pipeline
11.Pipeline Stage View
12.Eclipse Temurin Installer

Create Docker Credentials:
-Go to Manage Jenkins > Manage Credentials > (global) > Add Credentials.
-Choose Username with password as the kind.
-ID: docker-cred
-Username: Your Docker Hub username.
-Password: Your Docker Hub password.
-Click OK.


Create GitHub Credentials:
-Go to Manage Jenkins > Manage Credentials > (global) > Add Credentials.
-Choose Secret text as the kind.
-ID: git-cred
-Secret: Your GitHub Personal Access Token.
-Click OK.

Step 5 .Creating Pipeline

pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/vinodkumarkb/full-Stack-Blogging-App.git'
            }
        }
        
        stage('compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
        stage('Trivy FS Scan') {
            steps {
                sh "trivy fs --format table -o fs.html ."
            }
        }

         stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-app -Dsonar.projectKey=Blogging-app \
                      -Dsonar.java.binaries=target '''
    
                   }
            }
        }

        stage('Build') {
            steps {
                sh "mvn package"
            }
        }
        
        stage('Publish Artifacts') {
            steps {
                withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                 sh "mvn deploy"
                }
            }    
        }
        
        stage('Docker Build & Tag') {
            steps {
                script{
                withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                   
                sh "docker build -t vinodkb007/bloggingapp ."
                }
                }
            }
        }
        
         stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format table -o image.html vinodkb007/bloggingapp:latest"
            }
        }
        
         stage('Docker Push Image') {
            steps {
                script{
                withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                   
                sh "docker push vinodkb007/bloggingapp:latest"
                }
                }
            }
        }
        
        stage('k8 Deploy') {
            steps {
               withKubeConfig(caCertificate: '', clusterName: 'vinod', contextName: '', credentialsId: 'k8-cred', namespace: 'blogging-app', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.8.146:6443') {
                        sh "kubectl apply -f deployment-service.yaml"
                }
            }
        }
        
        stage('Verify the Deployment') {
            steps {
               withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'blogging-app', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.8.146:6443') {
                        sh "kubectl get pods -n blogging-app"
                        sh "kubectl get svc -n blogging-app"
                }
            }
        }
        
        
     post {
     always {
        script {
            def jobName = env.JOB_NAME
            def buildNumber = env.BUILD_NUMBER
            def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
            def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'

            def body = """
                <html>
                <body>
                <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                <h2>${jobName} - Build ${buildNumber}</h2>
                <div style="background-color: ${bannerColor}; padding: 10px;">
                <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                </div>
                <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                </div>
                </body>
                </html>
            """

            emailext (
                subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                body: body,
                to: 'vinod.kb1126@gmail.com',
                from: 'jenkins@example.com',
                replyTo: 'jenkins@example.com',
                mimeType: 'text/html',
                attachmentsPattern: 'trivy-image-report.html'
            )
        }
    }
}
}
}

