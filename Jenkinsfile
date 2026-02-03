pipeline {
    agent any

    tools {
        maven 'maven'   // ✅ FIXED (must match Jenkins tool name)
    }

    environment {
        BACKEND_IMAGE = "abhinaysk/backend"
        DOCKER_FILE   = "./Dockerfile"
    }

    stages {

        stage("Checkout Code") {
            steps {
                git(
                    url: 'https://github.com/Abhinaysk/mern-3tier.git',
                    branch: 'main',
                    credentialsId: 'git_hub'
                )
            }
        }

        stage("Debug Workspace") {
            steps {
                dir('./mern_3tire-main/backend')  {
                sh '''
                    pwd
                    ls -R .
                    ls -lrt
            
                '''
            }
        }
        }

        stage("SonarQube Analysis") {
            steps {
                dir('./mern_3tire-main/backend') {
                    withCredentials([
                        string(credentialsId: 'sonar-backend', variable: 'SONAR_TOKEN')
                    ]) {
                        sh '''
                          
                          sonar-scanner \
                            -Dsonar.login=$SONAR_TOKEN
                            
                        '''
                    }
                }
            }
        }

        stage("Build and Push Image") {
            steps {
                script {
                    dir('./mern_3tire-main/backend') {
                    def customImage = docker.build("${BACKEND_IMAGE}:${env.BUILD_NUMBER}", "-f ${DOCKER_FILE} .")

                    docker.withRegistry(
                        'https://registry.hub.docker.com',
                        'dockerhub-credentials'
                    ) {
                        customImage.push("${env.BUILD_NUMBER}")
                        customImage.push("latest")
                    }
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
