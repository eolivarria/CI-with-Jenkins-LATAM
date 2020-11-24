pipeline {
    agent any
    environment {
        //Docker Hub username
        DOCKER_IMAGE_NAME = "eolivarria/tomcat01"
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
       
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
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
