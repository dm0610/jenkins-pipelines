
//Пайплайн удалённого запуска параметризованного multibranch-пайплайна

def HARBOR_IS_AVAILABLE = true
def JENKINS_JOB_IS_AVAILABLE = true

def CRUMB = ""

def PARRENT_JOB = "job/mbp-test1" 
def TAG_JOB = "/job/dev"    ./по идее работает и на обычном пайплайне - просто TAG_JOB надо указывать пустым: ""

def USER_NAME = "user"
def PASSWD = "1qaz2wsx"

def API_KEY = "11244bd7b5e06718b0967446b8f99b0077" //это всё равно локальный домашний ключ, который ничего не даёт. И он давно удалён
def TARGET_HOST = "192.168.1.41"

def JOB_STATE = ""
//def MBP = "Yes"
pipeline {
    agent any
//    parameters {
//    }

    stages {
    //пустой и ненужный шаг, как выяснилось. 
        stage('VALIDATE PARAMETERS') {
            steps {
                script {
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
                    //Считываем CRUMB удаленного jenkins********************
                    CRUMB = sh (script: "curl -s --user ${USER_NAME}:${API_KEY} -X GET http://${TARGET_HOST}:8080/crumbIssuer/api/json | jq -r \".crumb\"", returnStdout: true).trim()
                    CRUMB="Jenkins-Crumb:${CRUMB}"
                    echo "this is CRUMB: ${CRUMB}"
                    //******************************************************
                    def LAST_BUILD = sh (script: "curl -s -H ${CRUMB} --user ${USER_NAME}:${API_KEY} -X GET http://${TARGET_HOST}:8080/${PARRENT_JOB}${TAG_JOB}/api/json | jq -r \".lastBuild\"", returnStdout: true).trim()
                    echo "${LAST_BUILD}"

                    //
                    while(true) {
                        if ("${LAST_BUILD}" == "null") {

                            def JOB_START = sh (script: "curl -s -H ${CRUMB} --user ${USER_NAME}:${API_KEY} -X POST http://${TARGET_HOST}:8080/${PARRENT_JOB}${TAG_JOB}/build", returnStdout: true).trim()

                            while ("${LAST_BUILD}" == "null") {
                                LAST_BUILD = sh (script: "curl -s -H ${CRUMB} --user ${USER_NAME}:${API_KEY} -X GET http://${TARGET_HOST}:8080/${PARRENT_JOB}${TAG_JOB}/api/json | jq -r \".lastBuild\"", returnStdout: true).trim()

                                sleep 5
                            } 

                            JOB_RES = sh (script: "curl -s -H ${CRUMB} --user ${USER_NAME}:${API_KEY} -X GET http://${TARGET_HOST}:8080/${PARRENT_JOB}${TAG_JOB}/lastBuild/api/json | jq -r \".result\"", returnStdout: true).trim()
                            JOB_STATE = sh (script: "curl -s -H ${CRUMB} --user ${USER_NAME}:${API_KEY} -X GET http://${TARGET_HOST}:8080/${PARRENT_JOB}${TAG_JOB}/api/json | jq -r \".color\"", returnStdout: true).trim()
                            echo "This is JOB_RES: ${JOB_RES}"
                            echo "This is JOB_STATE: ${JOB_STATE}"
                            
                            if ( "${JOB_STATE}" == "aborted" || "${JOB_STATE}" == "red" ){
                                break
                            } else {
                                continue
                            }

                        } 

                        else if ("${LAST_BUILD}" != "null") {

                            def JOB_START = sh (script: "curl -s -H ${CRUMB} --user ${USER_NAME}:${API_KEY} -X POST http://${TARGET_HOST}:8080/${PARRENT_JOB}${TAG_JOB}/buildWithParameters \
                                --data STOP_PODS=\'true\' \
                                --data BACKUP_DB=\'false\'", returnStdout: true).trim()
                            sh "sleep 5"

                            while (true) {
                                sleep 5
                                JOB_STATE = sh (script: "curl -s -H ${CRUMB} --user ${USER_NAME}:${API_KEY} -X GET http://${TARGET_HOST}:8080/${PARRENT_JOB}${TAG_JOB}/api/json | jq -r \".color\"", returnStdout: true).trim()
                                echo "JOB_STATE: ${JOB_STATE}"
                                if ("${JOB_STATE}" != "blue" && "${JOB_STATE}" != "aborted" && "${JOB_STATE}" != "red")  {
                                    continue
                                } else {
                                    sleep 10
                                    JOB_RES = sh (script: "curl -s -H ${CRUMB} --user ${USER_NAME}:${API_KEY} -X GET http://${TARGET_HOST}:8080/${PARRENT_JOB}${TAG_JOB}/lastBuild/api/json | jq -r \".result\"", returnStdout: true).trim()
                                    echo "This is JOB_RES: ${JOB_RES}"
                                    echo "*******************************************PIPELINE-LOG**********************************************************"
                                    sh """curl -s -H ${CRUMB} --user ${USER_NAME}:${API_KEY} -X GET http://${TARGET_HOST}:8080/${PARRENT_JOB}${TAG_JOB}/lastBuild/consoleText"""
                                    echo "*****************************************END-PIPELINE-LOG********************************************************"
                                    break
                                }
                            }
                            break
                        }
                    }
                }
            }
        }
    }
}
