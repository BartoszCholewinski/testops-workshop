/* groovylint-disable DuplicateStringLiteral, NoDef */
// HELPERS AND CONSTANTS
/*def suiteMap = []
def getHashmapKeys(map) {
    def list = []
    map.keySet().each {
        list << it
    }
    return list
}

def getPlansNames(map) {
    def list = []
    map.keySet().each {
        list << "${it} Automation"
    }
    return list
}
def getTestsSummary(file="results/${REPORT_ID}-test-output.xml") {
    def awkCommand = { position -> "awk -F \'\"\' \'NR==2 {print \$${position}}\' ${file}" }
    def failuresCount = sh(script:"${awkCommand(8)}", returnStdout:true)
    def totalCount = sh(script:"${awkCommand(6)}", returnStdout:true)
    def passedCount = sh(script:"echo \$((${totalCount}-${failuresCount}))", returnStdout:true)
    return [totalCount: totalCount, failuresCount: failuresCount, passedCount: passedCount]
}

def secrets = []

def plansList = getPlansNames(suiteMap)

    // ENVIRONMENT VARIABLES BLOCK FOR TESTRAIL
    env.TESTLINK_SUITE_ID = suiteMap."${params.TESTLINK_SUITE_NAME}"
    env.TESTLINK_ENABLED = params.TESTLINK_ENABLED
    env.TESTLINK_PROJECT_NAME = "TestProject"
    env.TESTLINK_PLAN_NAME = params.TESTLINK_PLAN_NAME
*/
//

pipeline {
    agent any
    environment {
        DOCKER_IMAGE_TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
    }
    stages {
        // build
        stage('Initial stage') {
            steps {
                dir('docker_introduction/docker-compose/') {
                    script {
                        sh("docker-compose build --build-arg DOCKER_IMAGE_TAG=${DOCKER_IMAGE_TAG}")
                    }
                }
            }
        }

        // launch app
        stage('Launch app') {
            steps {
                    dir('docker_introduction/docker-compose/') {
                        script {
                            sh('docker-compose up -d')
                        }
                    }
            }
        }

        // run tests

        stage('Run tests') {
            agent {
                docker {
                   image 'cypress/included:12.2.0' 
                   args '--network host --entrypoint=\'\''
                }
            }
            steps {
                dir('pipeline_ex/') {
                    script {
                        sh 'npm install'
                        try {
                            sh 'CYPRESS_BASE_URL=localhost:3000 cypress run'
                        }
                        catch (err) {
                            print(err)
                        }
                    }
                }
 
 
            }
        }
    }
    post {
        always {
            dir('docker_introduction/docker-compose/') {
                script {
                    sh('docker-compose down')
                }
            }
        }
    }
}
