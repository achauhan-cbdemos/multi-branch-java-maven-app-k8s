pipeline {
  agent { 
    kubernetes {
      label 'maven-alpine-pod'
       yamlFile 'mvn-pod.yaml'
     }
  }  
  stages {
    stage ('Build') {
      steps {
        container ('maven') {
          sh 'mvn -B -DskipTests clean package'
        }
      }
    }
    stage ('Test') {
      parallel {
        stage ('Test on Linux') {
          steps {
            container ('maven') {
              sh 'mvn test'
            }
          }
          post {
            always {
              junit 'target/surefire-reports/*.xml'
            }
          }
        }
        stage ('Test on Windows') {
          steps {
            echo 'Windows test..."
          }
          post {
            always {
              echo 'Windows test...post step'
            }
          }
        }
      }
    }
    stage ('Deliver for development') {
      when {
        branch 'development' 
      }
      steps {
        container ('maven') {
          input message: 'Deploy to Dev? (Click "Proceed" to continue)'
          sh './jenkins/scripts/deliver.sh'
        }
      }
    }
    stage ('Deliver for production') {
      when {
        branch 'production' 
      }
      steps {
        container ('maven') {
          input message: 'Deploy to Prod? (Click "Proceed" to continue)'
          sh './jenkins/scripts/deliver.sh'
        }
      }
    }          
  }
}
