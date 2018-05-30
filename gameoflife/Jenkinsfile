// Example Pipeline: Deploy to Elastic Beanstalk
pipeline {
    agent any
    environment {
        SNOW_AUTH = credentials('snow_auth')
        SNOW_URL = "${env.SNOW_URL}"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                sh "/usr/bin/mvn -e -X clean install sonar:sonar -Dskip-tests -Dsonar.host.url=http://jenkins.agile.env.ecs.digital:9000"
            }
        }
        stage('Test Verify') {
            post {
                failure {
                    echo "RESULT: failed"
                }
                fixed {
                    sh "curl -v -H \"Accept: application/json\" -H \"Content-Type: application/json\" -X POST --data '{\"short_description\":\"Test incident creation through REST\", \"comments\":\"These are my comments - fixed\"}' -u \"$SNOW_AUTH\" \"https://${SNOW_URL}.service-now.com/api/now/v1/table/incident\""
                }
                regression {
                    sh "curl -v -H \"Accept: application/json\" -H \"Content-Type: application/json\" -X POST --data '{\"short_description\":\"Test incident creation through REST\", \"comments\":\"These are my comments - regression\"}' -u \"$SNOW_AUTH\" \"https://${SNOW_URL}.service-now.com/api/now/v1/table/incident\""
                }
                success {
                    sh "curl -v -H \"Accept: application/json\" -H \"Content-Type: application/json\" -X POST --data '{\"short_description\":\"Test incident creation through REST\", \"comments\":\"These are my comments - success\"}' -u \"$SNOW_AUTH\" \"https://${SNOW_URL}.service-now.com/api/now/v1/table/incident\""
                }
            }
            steps {
                echo 'Testing..'
                sh "/usr/bin/mvn clean verify sonar:sonar -Dsonar.host.url=http://jenkins.agile.env.ecs.digital:9000"
            }
        }
    }
}