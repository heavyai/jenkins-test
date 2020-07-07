def precommit_container_image = "sloria/pre-commit"
def precommit_container_name = "pymapd-precommit-$BUILD_NUMBER"
def db_container_image = "omnisci/core-os-cuda-dev:master"
//def db_container_image = "omnisci/core-os-cuda"
def db_container_name = "pymapd-db-$BUILD_NUMBER"
def testscript_container_image = "rapidsai/rapidsai:0.8-cuda10.0-runtime-ubuntu18.04-gcc7-py3.6"
def testscript_container_name = "pymapd-pytest-$BUILD_NUMBER"
def stage_succeeded
def git_commit

void setBuildStatus(String message, String state, String context, String commit_sha) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/omnisci/jenkins-test"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: context],
      commitShaSource: [$class: "ManuallyEnteredShaSource", sha: commit_sha],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}

pipeline {
    agent none
    options { skipDefaultCheckout() }
    stages {
        stage('Set pending status') {
            agent any
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        checkout scm
                        script { git_commit = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%H'").trim() }
                    } else {
                        script { sh "echo else" }
                    } 
                }
                // Set pending status manually for all jobs before node is started
                setBuildStatus("Build queued", "PENDING", "Test", git_commit)
                sh """
                    echo setup
                """
            }
        }
        stage("Linter and Tests") {
            agent any
            stages {
                stage('Checkout') {
                    steps {
                        checkout scm
                        //script { git_commit = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%H'").trim() }
                    }
                }
                stage('Test') {
                    steps {
                        catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                            script { stage_succeeded = false }
                            setBuildStatus("Running tests", "PENDING", "$STAGE_NAME", git_commit);
                            sh """
                                echo test
                            """
                            script { stage_succeeded = true }
                        }
                    }
                    post {
                        always {
                            script {
                                if (stage_succeeded == true) {
                                    setBuildStatus("Build succeeded", "SUCCESS", "$STAGE_NAME", git_commit);
                                } else {
                                    sh """
                                        echo fail
                                    """
                                    setBuildStatus("Build failed", "FAILURE", "$STAGE_NAME", git_commit);
                                }
                            }
                        }
                    }
                }
            }
            post {
                always {
                    sh """
                        echo post
                    """
                    cleanWs()
                }
            }
        }
    }
}
