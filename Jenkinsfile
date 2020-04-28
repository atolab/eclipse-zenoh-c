pipeline {
  agent {
    kubernetes {
      label 'zenoh-agent-pod'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven
    image: maven:alpine
    command:
    - cat
    tty: true
  - name: zenoh-dev
    image: adlinktech/eclipse-zenoh-dev
    command:
    - cat
    tty: true
"""
    }
  }
  stages {
    stage('Run maven') {
      steps {
        container('maven') {
          sh '''
            uname -a
            pwd
            echo $PATH
            ls -al /opt/public/common/ || true
            ls -al /opt/public/common/cmake* || true
            ls -al /shared/common/ || true
            ls -al /shared/common/cmake*  || true
            ls -al /usr/bin/  || true
            ls -al /usr/local/bin/  || true
            docker -v || true
            docker images || true
          '''
        }
        container('zenoh-dev') {
          sh '''
            uname -a
            pwd
            echo $PATH
            ls -al /opt/public/common/ || true
            ls -al /opt/public/common/cmake* || true
            ls -al /shared/common/ || true
            ls -al /shared/common/cmake*  || true
            ls -al /usr/bin/  || true
            ls -al /usr/local/bin/  || true
            docker -v || true
            docker images || true
          '''
        }
      }
    }
  }
}