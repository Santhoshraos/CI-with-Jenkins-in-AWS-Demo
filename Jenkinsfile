pipeline {
    agent any
    environment {
        PROJECT_ID = 'my-assignments-277205'
        CLUSTER_NAME = 'kube-demo'
        LOCATION = 'asia-east1-a'
        CREDENTIALS_ID = 'kubernetes'
}
stages {
   stage("Checkout code") {
       steps {
           checkout scm
       }
   }
           stage("Build") {
       steps {
          echo "cleaning and packaging"
                    sh 'mvn clean package'
       }
  }
  stage("Test") {
      steps {
         echo "Testing"
                  sh 'mvn test'
      }
  }
  stage("Build image") {
     steps {
        script {
            myapp = docker.build("raosanthos/kube8s:${env.BUILD_ID}")
        }
    }
  }
  stage("Push image") {
     steps {
        script {
            docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
            myapp.push("${env.BUILD_ID}")
        }
     }
  }
}
stage('Deploy to GKE') {
    steps{
               echo "Deployment started"
               sh 'ls -ltr'
               sh 'pwd'
         sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yml"
         step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID,
clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yml',
credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
                      echo "Deployment Finished"
         }
      }
   }
}
