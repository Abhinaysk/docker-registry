pipeline {
    agent any

    tools {
        maven 'maven'   // ✅ FIXED (must match Jenkins tool name)
    }

    environment {
        BACKEND_IMAGE = "abhinay/backend"
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

        stage("SonarQube Analysis") {
            steps {
                dir('backend') {
                    withCredentials([
                        string(credentialsId: 'sonar-backend', variable: 'SONAR_TOKEN')
                    ]) {
                        sh '''
                          pwd
                          ls -lrt
                          sonar-scanner \
                            -Dsonar.host.url=http://3.110.215.9:9000 \
                            -Dsonar.login=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }

        stage("Build and Push Image") {
            steps {
                script {
                    def customImage = docker.build(
                        "${BACKEND_IMAGE}:${env.BUILD_NUMBER}",
                        "-f ${DOCKER_FILE} ."
                    )

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

    post {
        always {
            cleanWs()
        }
    }
}
