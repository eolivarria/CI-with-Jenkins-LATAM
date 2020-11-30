pipeline {
    agent any
    environment {
        //Docker Hub username
        DOCKER_IMAGE_NAME = "eolivarria/devops:${env.BUILD_ID}"
        PROJECT_ID = 'soy-reporter-289518'
        CLUSTER_NAME = 'kubernetes'
        LOCATION = 'us-west4-c'
        CREDENTIALS_ID = 'gke'
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
                }
            }
        }
     stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            //myapp.push("latest")
                            //myapp.push("${env.BUILD_ID}")
                        appimage.push("${env.BUILD_ID}")
                    }
                }
            }
        }
    stage('Deploy to GKE cluster') {
            steps{
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
                echo "Deployment to Kubernetes cluster completed.."
            }
        }
        
    }
}
