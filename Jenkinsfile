pipeline {
  agent any
  stages {
    stage('Unit Tests') {
      steps {
        node('host'){
            checkout scm
            sh 'cd ./springBootDemo && /usr/local/bin/docker-compose -f docker-compose-test.yml up spring_test && /usr/local/bin/docker-compose -f docker-compose-test.yml up -d mysql_test'
        }
      }
      post {
        failure {
          cleanWs()
        }
      }
    }
    stage('Integration Tests') {
      steps {
        node('host'){
            sh './mvnw package && /usr/local/bin/docker-compose -f docker-compose-integration-test.yml up spring_integration_test node_integration_test && /usr/local/bin/docker-compose -f docker-compose-integration-test.yml up -d mysql_integration_test mysql_service spring_service'
        }
      }
      post {
        always {
          node('host'){
            sh '/usr/local/bin/docker-compose -f docker-compose-integration-test.yml stop && /usr/local/bin/docker-compose rm spring_service'
          }
        }
        failure {
            node('host'){
                sh '/usr/local/bin/docker-compose -f docker-compose-integration-test.yml up spring_integration_test node_integration_test && /usr/local/bin/docker-compose -f docker-compose-integration-test.yml up -d mysql_integration_test mysql_service spring_service'
            }
          cleanWs()
        }
      }
    }
  }
}