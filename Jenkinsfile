pipeline {
    agent any 
    stages {
        stage('Clone') {
            steps {
                echo "checking out the repo"
                git 'https://github.com/vathsalahn/jenkins-demo.git'
            
            }
        }
        stage('build'){
            steps {
                echo "building the project"
                dir('MavenProject') {
                    bat '''set M2_HOME=C:\\Program Files\\apache-maven-3.6.3
                    set path=C:\\Program Files\\apache-maven-3.6.3\\bin;%path%,
                    mvn clean install'''
                }
                
            }
        }
        
        stage('Archieve Artifacts'){
            steps {
                echo "archiving the artifacts"
                archiveArtifacts 'MavenProject/multi3/target/*.war'
            }
            
        }
        stage('Deployment') {
            // Deployment
            steps {
                script {
                    echo "deployment"
                    bat 'cp MavenProject/multi3/target/*.war /Applications/apache-tomcat-7.0.88/webapps/'
                }
            }
        }
        stage('publish html report') {
            steps{
                echo "publishing the html report"
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
            }
        }
        stage('clean up') {
            steps {
                echo "cleaning up the workspace"
                cleanWs()
            }
        }
        stage("Metrics"){
            steps{
            parallel ( "JavaNcss Report":   
            {
              node('window'){
                git 'https://github.com/vathsalahn/jenkins-demo.git'
                bat 'cd javancss-master'
                bat 'mvn test javancss:report'
                }
            },
            "FindBugs Report" : {
            node('window'){
                bat 'mkdir javancss1 ; cd javancss1 ;pwd'
                git 'https://github.com/vathsalahn/jenkins-demo.git'
                bat 'cd javancss-master ; mvn findbugs:findbugs ; pwd'
                deleteDir()
                }

              }
         )
            }
         post{
                success {
                    emailext body: 'Successfully completed pipeline project with archiving the artifacts', subject: 'Pipeline was successfull', to: $NOTIFICATION_EMAIL
                }
    }
}
        
    }
}
