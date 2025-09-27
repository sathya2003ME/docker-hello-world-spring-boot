node {
    // reference to JDK 21
    def jdkHome = tool name: 'jdk-21', type: 'jdk'

    // set JAVA_HOME and PATH to JDK 21
    env.JAVA_HOME = jdkHome
    env.PATH = "${jdkHome}/bin:${env.PATH}"

    // reference to Maven
    def mvnHome = tool 'maven-3.8.8'

    def dockerImage
    def dockerRepoUrl = "localhost:8083"
    def dockerImageName = "hello-world-java"
    def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:${env.BUILD_NUMBER}"
    
    stage('Clone Repo') {
        git 'https://github.com/dstar55/docker-hello-world-spring-boot.git'
        mvnHome = tool 'maven-3.8.5'
    }    
  
    stage('Build Project') {
        sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
    }
	
    stage('Publish Tests Results'){
        parallel(
            publishJunitTestsResultsToJenkins: {
                echo "Publish junit Tests Results"
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
            },
            publishJunitTestsResultsToSonar: {
                echo "This is branch b"
            }
        )
    }
		
    stage('Build Docker Image') {
        sh "whoami"
        sh "mv ./target/hello*.jar ./data" 
        dockerImage = docker.build("hello-world-java")
    }
   
    stage('Deploy Docker Image'){
        echo "Docker Image Tag Name: ${dockerImageTag}"

        sh "docker login -u admin -p admin123 ${dockerRepoUrl}"
        sh "docker tag ${dockerImageName} ${dockerImageTag}"
        sh "docker push ${dockerImageTag}"
    }
}

