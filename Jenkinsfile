pipeline {
  agent {
    kubernetes {
      label 'zenoh-agent-pod'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: zenoh-dev
    image: adlinktech/eclipse-zenoh-dev
    volumeMounts:
    - name: dockersock
      mountPath: "/var/run/docker.sock"
    command:
    - cat
    tty: true
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock  

"""
    }
  }
  stages {
    stage('Build zenoh-c') {
      steps {
        container('zenoh-dev') {
          sh '''
            uname -a
            pwd
            ls -al
            git log --graph --date=short --pretty=tformat:'%ad - %h - %cn -%d %s' -n 20 || true
            docker -v || true
            docker images || true
            make all || true
            make all-cross
          '''
        }
      }
    }
  }
}