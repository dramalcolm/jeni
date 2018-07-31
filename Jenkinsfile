pipeline {
    agent any
   
    tools {
        maven 'LocalMaven'
    }

    parameters{
        string(name: 'tomcat_dev', defaultValue: '13.58.107.148', description: 'Tomcat Staging Server')
        string(name: 'tomcat_prod', defaultValue: '13.59.203.150', description: 'Tomcat Production Server')
    }

    triggers{
        pollSCM('* * * * *')
    }

    stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /Users/Shared/Jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /Users/Shared/Jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }

}