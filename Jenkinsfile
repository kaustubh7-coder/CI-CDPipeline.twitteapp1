pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = 'kaustubhaws/cicdpipeline'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/kaustubh7-coder/CI-CDPipeline.twitteapp1.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=CI-CD-Pipeline1 \
                        -Dsonar.projectName=CI-CD-Pipeline1 \
                        -Dsonar.java.binaries=target
                    '''
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Publish Artifacts') {
            steps {
                withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', traceability: true) {
                    sh "mvn deploy"
                }
            }
        }

        stage('Docker Build & Tag') {
            steps {
                script {
                    def imageTag = "${env.BUILD_NUMBER}"
                    sh """
                        docker build -t ${DOCKER_IMAGE}:${imageTag} .
                        docker tag ${DOCKER_IMAGE}:${imageTag} ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    def imageTag = "${env.BUILD_NUMBER}"
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh '''
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                            docker push ${DOCKER_IMAGE}:latest
                        '''
                    }
                }
            }
        }

        stage('Greeting') {
            steps {
                echo 'Hello from Jenkins!'
            }
        }

        stage('Wrap Up') {
            steps {
                echo 'Pipeline completed successfully.'
            }
        }
    }
}
