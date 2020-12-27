node('maven-label') {
    def mvnHome
    stage('Preparation') { 
        git 'https://github.com/ip-card/qfapp.git'
        
        mvnHome = tool 'maven-3.6.3'
    }
   stage("build & SonarQube analysis") {
         
              withSonarQubeEnv('sonarqube') {
                 sh '"$MVN_HOME/bin/mvn" clean package sonar:sonar'
              }
 
      }

      stage("Quality Gate"){
          timeout(time: 1, unit: 'MINUTE') {
              def qg = waitForQualityGate()
              if (qg.status != 'OK') {
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
              }
          }
      }
    stage('Build') {
        // Run the maven build
        withEnv(["MVN_HOME=$mvnHome"]) {
            if (isUnix()) {
                sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean deploy sonar:sonar -Dsonar.host.url=http://ec2-52-33-50-210.us-west-2.compute.amazonaws.com:9000/'
            } else {
                bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)
            }
        }
    }
    
    stage('Results') {
        junit '**/target/surefire-reports/TEST-*.xml'
        archiveArtifacts 'target/*.jar'
    }
}
