node('maven-label') {
    def mvnHome
    stage('Preparation') { 
        git 'https://github.com/ip-card/qfapp.git'
        
        mvnHome = tool 'maven-3.6.3'
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
     stage("sonar-qualitygate"){
	    withCredentials([string(credentialsId: 'sonar_token', variable: 'sonar_token')]) {
	    sh 'sh breakbuild.sh http://ip-172-31-47-61.us-east-2.compute.internal:9000 "$sonar_token"'
		    
	    }
    }
    stage('Results') {
        junit '**/target/surefire-reports/TEST-*.xml'
        archiveArtifacts 'target/*.jar'
    }
}
