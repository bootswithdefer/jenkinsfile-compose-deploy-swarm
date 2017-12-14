#!groovy
pipeline { 
  agent {
    docker {
      label 'docker'
      image 'bootswithdefer/docker-compose:latest'
      args '-v /var/run/docker.sock:/var/run/docker.sock -e USER=jenkins --group-add docker'
      reuseNode true
    }
  }
  
  options {
    buildDiscarder(logRotator(numToKeepStr: '20'))
  }

  stages {
    stage('Deploy') {
      steps {
        script {
          withCredentials([dockerCert(variable: 'DOCKER_CERT_PATH',
                                      credentialsId: 'ucp_jenkins_sandbox')]) {
              sh """export DOCKER_HOST=tcp://10.120.120.70:443
                    export DOCKER_TLS_VERIFY=1
                    docker version
                    export DOMAIN="test.domain"
                    docker stack deploy -c docker-compose.hrm.http.yml hrm-http-example"""
          }
        }
      }
    }      
  }
}
