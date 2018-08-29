pipeline {
    agent any
    tools { 
        maven 'local_maven' 
        jdk 'local_java' 
    }

    stages{
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "MAVEN_HOME = ${MAVEN_HOME}"
                ''' 
            }
        }

        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving....'
                    archiveArtifacts artifacts: '**/target/*.war'
                } 
            }
        }

        stage ('Deploy to Staging'){
            steps {
                build job: 'pipeline_deploy_to_stg'
            }
        }

        stage('Deploy to Production'){
            steps {
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve Production Deployment?'
                }

                build job: 'pipeline_deploy_to_prod'
            }
            post {
                success {
                    echo 'Release deployed to production'
                }
                
                failure {
                    echo 'Production Release Failed'
                }
            }
        }
    }
}