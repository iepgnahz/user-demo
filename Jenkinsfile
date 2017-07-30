pipeline {
  agent any
  stages {
    stage('Build'){
      steps {
        node('host'){
            checkout scm
            sh 'git submodule init && git submodule update'
        }
      }
    }
    stage('Unit Tests') {
      steps {
        node('host'){
            sh 'cd ./test-demo && /usr/local/bin/docker-compose -f docker-compose-test.yml up spring_test && /usr/local/bin/docker-compose -f docker-compose-test.yml up -d mysql_test'
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
        node('host') {
            sh 'cd ./test-demo && ./mvnw package && /usr/local/bin/docker-compose -f docker-compose-integration-test.yml up spring_integration_test node_integration_test && /usr/local/bin/docker-compose -f docker-compose-integration-test.yml up -d mysql_integration_test mysql_service spring_service'
        }
      }
      post {
        always {
          node('host'){
            sh '/usr/local/bin/docker rm spring_service -f'
          }
        }
        failure {
          cleanWs()
        }
      }
    }
  }
}