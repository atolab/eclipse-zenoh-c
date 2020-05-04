pipeline {
  agent { label 'zenoh' }
  stages {
    stage('Build zenoh-c') {
      steps {
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