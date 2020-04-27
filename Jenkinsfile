pipeline {
    agent any
    tools {
        cmake 'cmake-latest'
    }
    stages {
        stage('Explore env') {
            steps {
                sh '''
                    uname -a
                    pwd
                    echo $PATH
                    ls -al /shared/common/
                    ls -al /shared/common/cmake*
                    ls -al /usr/bin/
                    ls -al /usr/local/bin/
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                    make all
                    make all-cross
                '''
            }
        }
    }
    post {
        // send a mail on unsuccessful and fixed builds
        unsuccessful { // means unstable || failure || aborted
            emailext subject: 'Build $BUILD_STATUS $PROJECT_NAME #$BUILD_NUMBER!', 
            body: '''Check console output at $BUILD_URL to view the results.''',
            recipientProviders: [culprits(), requestor()], 
            to: 'julien.enoch@adlinktech.com'
        }
        fixed { // back to normal
            emailext subject: 'Build $BUILD_STATUS $PROJECT_NAME #$BUILD_NUMBER!', 
            body: '''Check console output at $BUILD_URL to view the results.''',
            recipientProviders: [culprits(), requestor()], 
            to: 'julien.enoch@adlinktech.com'
        }
    }
}