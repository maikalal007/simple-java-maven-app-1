pipeline {
    agent {
      docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
          }
      }
    stages {
        stage('Build') { 
        agent {
          docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
          }
        } 

            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage('install and sonar parallel') {
        agent any 
            steps {
                parallel(install: {
                    sh "mvn -U clean test cobertura:cobertura -Dcobertura.report.format=xml"
                }, sonar: {
                    sh "mvn sonar:sonar -Dsonar.host.url=${env.SONARQUBE_HOST} -Dsonar.login=biplab -Dsonar.password=biplab"
                })
            }

            post {
                always {
                    junit '**/target/*-reports/TEST-*.xml'
                    step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
                }
            }
	}        
        stage('Test') { 
            steps {
                sh 'mvn test' 
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml' 
                }
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
