pipeline {
    agent any

    tools {
        maven 'Maven'
    }
    
    environment{
        BACKEND_IMAGE= "Abhinay/backend"
        DOCKER_FILE = "./Dockerfile"
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
                dir('./backend') {
                    withCredentials([string(credentialsId: 'sonar-backend', variable: 'SONAR_TOKEN')]) {
                        sh '''
                        mvn sonar:sonar \
                          -Dsonar.host.url=http://3.110.215.9:9000 \
                          -Dsonar.login=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }
        // stage("build") {
        
        //     steps {
        //         sh """
        //         pwd
        //         ls -lrt
        //         docker build -t ${BACKEND_IMAGE}:${env.BUILD_NUMBER} -f ${DOCKER_FILE} .
        //         """
        //     } 
        
        //}
        stage("build and Push Image") {
            steps {
                script {

                    def customImage = docker.build("${BACKEND_IMAGE}:${env.BUILD_NUMBER}")
                    withCredentials([
                        usernamePassword(
                        credentialsId: 'dockerhub-credentials', 
                        usernameVariable: 'DOCKERHUB_USERNAME', 
                        passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    
                    // // Login to Docker Hub
                    // docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                    //     // Push the image to Docker Hub
                        customImage.push()
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
