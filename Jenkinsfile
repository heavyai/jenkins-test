pipeline {
    agent { label 'u18.04-980m-crusher-0_1' }
    stages {
        stage('Conda') {
            steps {
                sh '''
                    ls -la
                    touch hi
                    ls -la
                '''
            }
        }
        stage('Pip') {
            steps {
                sh '''
                    ls -la
                    touch hi2
                    ls -la
                '''
            }
        }
    }
    // post {
    //     always {
    //         sh 'tar czvf artifacts.tar.gz -C artifacts .'
    //         archiveArtifacts artifacts: 'artifacts.tar.gz'
    //         cleanWs()
    //     }
    // }
}
