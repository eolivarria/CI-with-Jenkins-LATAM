pipeline {
    agent any
    environment {
        //Docker Hub username
        DOCKER_IMAGE_NAME = "eolivarria/devops:${env.BUILD_ID}"
    }
    stages {
     stage('Checkout SCM') {
      steps {
       checkout scm
      }
     }
        
    stage('Build package') {
        steps {
        echo "Cleaning and packing.."
         sh 'mvn clean package'
        }
    }
        
     stage('Test') {
      steps {
       echo "Testing.."
       sh 'mvn test'
      }
     }
     stage("Build image") {
            steps {
                script {
                    appimage = docker.build(DOCKER_IMAGE_NAME)
                    //myapp = docker.build("DOCKER-HUB-USERNAME/hello:${env.BUILD_ID}")
                }
            }
        }
     stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            //myapp.push("latest")
                            //myapp.push("${env.BUILD_ID}")
                        appimage.push("${env.BUILD_NUMBER}")
                        appimage.push("latest")
                    }
                }
            }
        }         
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'deployment.yaml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}
