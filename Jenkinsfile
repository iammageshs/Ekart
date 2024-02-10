pipeline {
    agent any
    tools {
        // Define JDK and Maven tools
        jdk 'jdk-11'
        maven 'maven3'
        // Define Sonar Scanner tool
        tool 'sonar-scanner', 'sonar-scanner'
    }
    
    environment {
        // Define Sonar Scanner tool
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Git checkout') {
            steps {
                // Git checkout step
                git branch: 'main', credentialsId: 'git_id', url: 'https://github.com/iammageshs/Ekart.git'
            }
        }
        
        stage('Maven compile') {
            steps {
                // Maven compile step
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        stage('OWASP scan') {
            steps {
                // OWASP Dependency Check step
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                archiveArtifacts artifacts: '**/dependency-check-report.xml'
            }
        }
        
        stage('SonarQube') {
            steps {
                // SonarQube scan step
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=Devops-Shopping-Cart \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=Devops-Shopping-Cart'''
                }
            }
        }
        
        stage('Maven build') {
            steps {
                // Maven build step
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Docker build and push docker hub') {
            steps {
                // Docker build, tag, and push steps
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-credentials', toolName: 'docker') {
                        sh "docker build -t maxhunt/shopping-cart:latest -f docker/Dockerfile ."
                        sh "docker push maxhunt/shopping-cart:latest"
                    }
                }
            }
        }
        
        stage('Deploy to Docker') {
            steps {
                // Deploy to docker container
                script {
                    withDockerServer([uri: "tcp://docker-server:2376"]) {
                        sh "docker run -d --name shop-shop -p 9070:9070 maxhunt/shopping-cart:latest"
                    }
                }
            }
        }
    }
}
