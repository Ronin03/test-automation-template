pipeline {
    agent any
    parameters {
        choice(
            name: 'Environment',
            choices: ['dev', 'uat', 'prod']
        )
        choice(
            name: 'Tag',
            choices: ['Regression', 'Functional', 'Smoke', 'Unit']
        )
        choice(
            name: 'Browser',
            choices: ['CHROME', 'FIREFOX', 'EDGE']
        )
    }
    stages {
        stage('Build') {
            when {
                triggeredBy cause: 'UserIdCause'
            }
            steps {
                sh 'mvn clean install -DskipTests=true'
            }
        }
        stage('Run tests') {
            when {
                triggeredBy cause: 'UserIdCause'
            }
            steps {
                script {
                    try {
                        sh "mvn test -Denvironment=${params.Environment} -Dgroups=${params.Tag} -Dbrowser=${params.Browser} -Dgrid"
                    } catch (err) {
                        println err
                        currentBuild.result = 'FAILURE'
                    }
                }
            }
        }
        stage('Generate Allure report') {
            when {
                triggeredBy cause: 'UserIdCause'
            }
            steps {
                allure(
                    includeProperties: false,
                    results: [[path: 'target/allure-results']
                )
                echo('Report available in: ' + BUILD_URL + 'allure')
            }
        }
        stage('Clear Workspace') {
            steps {
                cleanWs()
            }
        }
    }
}