pipeline {
    agent {
        node {
            label 'nodejs-agent-v1'
        }
    }
    environment {
        JAVA_TOOL_OPTIONS="-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -Dfile.encoding=UTF8 -Duser.timezone=Europe/Rome"
        TZ="Europe/Rome"
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '50'))
    }
    stages {
        stage('Make') {
            steps {
                sh '. load_nvm && make all'
            }
        }
        stage('Packaging') {
            steps {
                archiveArtifacts artifacts: "dist/openchat.tgz", fingerprint: true
            }
        }
    }
    post {
        always {
            script {
                GIT_COMMIT_EMAIL = sh (
                    script: 'git --no-pager show -s --format=\'%ae\'',
                    returnStdout: true
                ).trim()
            }
            emailext attachLog: true, body: '$DEFAULT_CONTENT', recipientProviders: [requestor()], subject: '$DEFAULT_SUBJECT',  to: "${GIT_COMMIT_EMAIL}"
        }
    }
}
