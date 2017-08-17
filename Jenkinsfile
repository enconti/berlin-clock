pipeline {
    agent any

    tools {
        jdk 'jdk8'
        maven 'maven'
    }

    stages {
        stage('install and sonar parallel') {
            steps {
                parallel(install: {
                    sh "mvn -U clean test cobertura:cobertura -Dcobertura.report.format=xml"
                })
            }
            post {
                always {
                    junit '**/target/*-reports/TEST-*.xml'
                    step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
                }
            }
        }
        stage('Sonar') {
            steps {
                    sh "mvn sonar:sonar -Dsonar.host.url=${env.SONARQUBE_HOST}"
            }
        }
        stage ('deploy'){
            steps{
                configFileProvider([configFile(fileId: 'our_settings', variable: 'SETTINGS')]) {
                    sh "mvn -s $SETTINGS deploy -DskipTests -Dartifactory_url=${env.ARTIFACTORY_URL}"
                }
            }
        }
    }
}
