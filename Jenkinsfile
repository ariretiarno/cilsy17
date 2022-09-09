pipeline {
    agent any
    
    environment {
        GIT_COMMIT_SHORT = sh(returnStdout: true, script: '''echo $GIT_COMMIT | head -c 7''')
    }

    stages {
        stage('Prepare .env') {
            steps {
                sh 'echo GIT_COMMIT_SHORT=$(echo $GIT_COMMIT_SHORT) > .env'
            }
        }

        stage ('build Database') {
            steps {
                sh 'docker build -t ariretiarno/db-cilist:$GIT_COMMIT_SHORT database/.'
                sh 'docker push ariretiarno/db-cilist:$GIT_COMMIT_SHORT'
            }
        }

        stage ('build FE') {
            steps {
                sh 'docker build -t ariretiarno/backend-cilist:$GIT_COMMIT_SHORT backend/.'
                sh 'docker push ariretiarno/backend-cilist:$GIT_COMMIT_SHORT'
            }
        }

        stage ('build BE') {
            steps {
                sh 'docker build -t ariretiarno/frontend-cilist:$GIT_COMMIT_SHORT frontend/.'
                sh 'docker push ariretiarno/frontend-cilist:$GIT_COMMIT_SHORT'
            }
        }

        stage ('Deploy') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''docker compose up -d
                sleep 40
                docker compose restart backend''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '.env,docker-compose.yml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }

    }
}