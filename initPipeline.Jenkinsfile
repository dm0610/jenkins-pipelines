
def HARBOR_IS_AVAILABLE = true
def JENKINS_JOB_IS_AVAILABLE = true
def CRUMB = ""
def PARRENT_JOB = "job/test1/"
def TAG_JOB = "job/dev"
def USER_NAME = "user"
def PASSWD = "1qaz2wsx"
def API_KEY = "11244bd7b5e06718b0967446b8f99b0077"
pipeline {
    agent any
    parameters {
    }

    stages {
        stage('VALIDATE PARAMETERS') {
            steps {
                script {
                    
                    sh """
                        CRUMB=$(curl -s --user ${USER_NAME}:${API_KEY} -X GET http://${TARGET_HOST}:8080/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,':',//crumb))
                    """
                    for (true) {
                        curl -s -H "$CRUMB" --user ${USER_NAME}:${API_KEY} -X GET "http://${TARGET_HOST}:8080/${PARRENT_JOB}/lastBuild/api/json | jq -r '.result'"
                        break
                        
                    }
                    if (true) {
                        //error('RECREATE_PODS should be false if FRONTEND_STUB is true')
                        echo 'Check harbor availability ...'

                        echo 'Check jenkins availability ...'

                        echo 'Check gitlab availability ...'
                    }
                }
            }
        }

        stage('HARBOR REPLICATION') {
            when {
                expression {
                    HARBOR_IS_AVAILABLE == true
                }
            }
            steps {
                script {
                    echo 'Harbor is replicating ...'
                }
            }
        }

        stage('START REMOTE JENKINS JOB') {
            when {
                expression {
                    JENKINS_JOB_IS_AVAILABLE == true
                }
            }
            steps {
                script {
                    echo 'Starting remote jenkins job'
                }
            }
        }
    }
}
