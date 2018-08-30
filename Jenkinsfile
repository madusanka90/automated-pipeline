pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_stg', defaultValue: '34.207.68.248', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '34.207.70.227', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

    tools { 
        maven 'local_maven' 
        jdk 'local_java' 
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
                        echo "environment:${params.tomcat_stg}"
                        sh "scp **/target/*.war tomcat@${params.tomcat_stg}:'/opt/middleware/tomcat/apache-tomcat-8.5.33/webapps'"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        echo "environment:${params.tomcat_prod}"
                        sh "scp **/target/*.war tomcat@${params.tomcat_prod}:'/opt/middleware/tomcat/apache-tomcat-8.5.33/webapps'"
                    }
                }
            }
        }
    }
}
