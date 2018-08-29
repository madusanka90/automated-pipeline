pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '52.91.26.64', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '54.227.125.20', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

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


stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh 'sshpass -p "tomcat" scp test tomcat@${params.tomcat_stg}:/opt/middleware/tomcat/apache-tomcat-8.5.33/webapps'
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "sshpass -p "tomcat" scp test tomcat@${params.tomcat_prd}:/opt/middleware/tomcat/apache-tomcat-8.5.33/webapps"
                    }
                }
            }
        }
    }
}