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
     stage('Build and push Docker Image') {
      steps{
        script {
           //appimage = docker.build( "almitarosita/devops:${env.BUILD_ID}")
           //appimage = docker.build("gcr.io/vaulted-quarter-260801/devops:${env.BUILD_ID}")
            appimage = docker.build(DOCKER_IMAGE_NAME)
           //docker.withRegistry("https://registry.hub.docker.com",'docker-hub-credentials') 
           //docker.withRegistry('https://gcr.io','gcr:gcr'){ appimage.push("${env.BUILD_ID}")           }
            docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
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
