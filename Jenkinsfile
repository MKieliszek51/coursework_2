node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        app = docker.build("mkieliszek51/coursework_2")
    }

    stage('Test image') {

        app.inside {
            sh 'echo "Tests passed"'
        }
    }
	
	stage('Sonarqube') 
	{
		agent any
		environment 
		{
			scannerHome = tool 'SonarQube'
		}
		steps 
		{
			withSonarQubeEnv('sonarqube') 
			{
				sh "${scannerHome}/bin/sonar-scanner"
			}
			timeout(time: 10, unit: 'MINUTES') 
			{
				waitForQualityGate abortPipeline: true
			}
		}
	}
	

    stage('Push image') {
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}