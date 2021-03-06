pipeline {
  agent { label 'MacMini' }
  parameters {
    gitParameter name: 'TAG', 
                 type: 'PT_TAG',
                 defaultValue: 'master'
  }

  stages {
    stage('Checkout Git TAG') {
      steps {
        cleanWs()
        checkout([$class: 'GitSCM',
                  branches: [[name: "${params.TAG}"]],
                  doGenerateSubmoduleConfigurations: false,
                  extensions: [],
                  gitTool: 'Default',
                  submoduleCfg: [],
                  userRemoteConfigs: [[url: 'https://github.com/eclipse-zenoh/zenoh-c.git']]
                ])
      }
    }
    stage('Simple build') {
      steps {
        sh '''
        git log --graph --date=short --pretty=tformat:'%ad - %h - %cn -%d %s' -n 20 || true
        make all
        '''
      }
    }
    stage('Cross-platforms build') {
      steps {
        sh '''
        docker images || true
        make all all-cross
        '''
      }
    }
    stage('Deploy to download.eclipse.org') {
      steps {
        sshagent ( ['projects-storage.eclipse.org-bot-ssh']) {
          sh '''
          HOST="genie.zenoh@projects-storage.eclipse.org"
          DOWNLOAD_DIR="/home/data/httpd/download.eclipse.org/zenoh/zenoh-c/${TAG}"
          ssh ${HOST} rm -fr ${DOWNLOAD_DIR}
          ssh ${HOST} mkdir -p ${DOWNLOAD_DIR}

          for PLATFORM in `ls build/crossbuilds/`; do
            echo "Deploy for platform: ${PLATFORM}"
            ssh ${HOST} mkdir -p ${DOWNLOAD_DIR}/${PLATFORM}
            scp  build/crossbuilds/${PLATFORM}/libzenohc*.* build/crossbuilds/${PLATFORM}/z_* build/crossbuilds/${PLATFORM}/zn_* ${DOWNLOAD_DIR}/${PLATFORM}/
          done

          echo "Deploy for platform: OSX"
          ssh ${HOST} mkdir -p ${DOWNLOAD_DIR}/OSX
          scp  build/crossbuilds/${PLATFORM}/libzenohc*.* build/crossbuilds/${PLATFORM}/z_* build/crossbuilds/${PLATFORM}/zn_* ${DOWNLOAD_DIR}/OSX/

          echo "Deploy include files"
          tar czvf zenoh-c-includes.tgz include/
          scp  zenoh-c-includes.tgz ${DOWNLOAD_DIR}/
          '''
        }
      }
    }
  }

  post {
    success {
        archiveArtifacts artifacts: 'build/crossbuilds/*/*zenohc.*', fingerprint: true
    }
  }
}
