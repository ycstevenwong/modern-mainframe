pipeline {
    agent any

    environment {
        ZOWE_USER = credentials('ZOWE_USER')
        ZOWE_PASS = credentials('ZOWE_PASS')
        ZOWE_HOST = '204.90.115.200'
        HLQ = 'Z27387'
        PORT = '10443'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Upload COBOL to Mainframe') {
            steps {
                sh '''
                  zowe files upload file-to-data-set \
                  ./test.cbl \
                  "${HLQ}.CBL(TEST)" \
                  --host "$ZOWE_HOST" \
                  --port "$PORT" \
                  --user "$ZOWE_USER" \
                  --password "$ZOWE_PASS" \
                  --reject-unauthorized false
                '''
            }
        }
        stage('Submit JCL Job'){
            steps {
                sh '''
                  zowe jobs submit data-set \
                  "${HLQ}.JCL(ADDAMT)" \
                  --host "$ZOWE_HOST" \
                  --port "$PORT" \
                  --user "$ZOWE_USER" \
                  --password "$ZOWE_PASS" \
                  --reject-unauthorized false \
                  --wfo
                '''
            }
        }
    }
    post {
        success{
            echo 'Pipeline completed - job submitted successfully'
        }
        failure{
            echo 'Pipeline failed - check Zowe CLI'
        }
    }
}