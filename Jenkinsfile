pipeline{
    agent any
    parameters {
        booleanParam(name: 'skip_sonar', defaultValue: false, description: "Установите в true для того чтобы пропустить проверки сонара (они сейчас не работают)")
    }
    stages{
        stage("Sonar quality check"){
            when { 
                expression {
                    params.skip_sonar != true }
                }
            agent {
                docker {
                    image 'openjdk:11'
                    args '-v /var/run/docker.sock:/var/run/docker.sock  -u root:root'
                }
            }
            steps{
                echo "Start sonar quality check"
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube --stacktrace --warning-mode all'
                    }
                    timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }               
                }
            }
        }
    }

}