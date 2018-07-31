pipeline {
    agent any
   
    tools {
        maven 'LocalMaven'
    }

    parameters{
        string(name: 'tomcat_dev', defaultValue: '18.222.200.107', description: 'Tomcat Staging Server')
        string(name: 'tomcat_prod', defaultValue: '18.191.187.216', description: 'Tomcat Production Server')
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
                        sh "scp -i ~/Documents/GQ2.pem **/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i ~/Documents/GQ2.pem **/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }

}