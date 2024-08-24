pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/vegadelalyra/Ekart.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        // stage('OWASP Scan') {
        //     steps {
        //         dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
        //         dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //     }
        // }

        // stage('SonarQube') {
        //     steps {
        //         withSonarQubeEnv('sonar-server') {
        //             sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Shopping-Cart \
        //                 -Dsonar.java.binaries=. \
        //                 -Dsonar.projectKey=Shopping-Cart \
        //                 -Dsonar.sources=. '''
        //         }
        //     }
        // } 
        
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Docker') {
            steps {
                withDockerRegistry(credentialsId: 'bc4cd65d-1606-4a3a-8bbc-18529da9b45a', url: 'https://index.docker.io/v1/') {
                    sh "docker build -t shopping-cart -f docker/Dockerfile ."
                    sh "docker tag shopping-cart vegadelalyra/shopping-cart:latest"
                    sh "docker push vegadelalyra/shopping-cart:latest"
                }
            }
        }
    }
}
