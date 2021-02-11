pipeline {
    agent any

    parameters {
        booleanParam(name: 'FRONTEND_STUB', defaultValue: false, description: '')
        string(name: 'STOP_PODS', defaultValue: 'dummy', description: '')
        string(name: 'BACKUP_DB', defaultValue: 'dummy', description: '')
        string(name: 'RESTORE_DB', defaultValue: 'dummy', description: '')
        string(name: 'VAULT_SWITCH', defaultValue: 'dummy', description: '')
        string(name: 'STAND_DEPLOY', defaultValue: 'dummy', description: '')
    }

    stages {
        stage('VALIDATE BUILD_NUMBER') {
            steps {
                script {
                    if ( env.BUILD_NUMBER.toInteger() >1 ) {
                        echo "IT'S OK!"
                    } else {
                        currentBuild.result = 'ABORTED'
                        error('FIRSTBUILD')
                    }
                }
            }
        }
        stage('VALIDATE PARAMETERS') {
            steps {
                script {
                    if (true) {

                        echo "Check FRONTEND_STUB state: ${params.FRONTEND_STUB}"

                        echo "Check STOP_PODS state: ${params.STOP_PODS}"

                        echo "Check BACKUP_DB state: ${params.BACKUP_DB}"

                        echo "Check RESTORE state: ${params.RESTORE_DB}"

                        echo "Check VAULT_SWITCH state: ${params.VAULT_SWITCH}"

                        echo "Check STAND_DEPLOY state: ${params.STAND_DEPLOY}"

                    }
                }
            }
        }
    }
}
