pipeline {
    agent any

    environment {
        SONAR_HOME = tool 'sonar'
    }

    stages {

        stage("Clone Code from GitHub") {
            steps {
                git branch: "main",
                credentialsId: "github-cred",
                url: "https://github.com/Pratham446/DevSecOps.git"
            }
        }

        stage("Sonarqube Quality Analysis") {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh """
                    ${SONAR_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=wanderlust \
                    -Dsonar.projectKey=wanderlust
                    """
                }
            }
        }

        stage("OWASP Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }


        stage("Sonar Quality Gate Scan") {
            steps {
                timeout(time: 2, unit: "MINUTES") {
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("Trivy File System Scan") {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage("Deployment using docker") {
            steps {
                sh "docker-compose up -d"
            }
        }       

    }
}
