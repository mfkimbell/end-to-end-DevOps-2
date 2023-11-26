pipeline 
{
    agent 
	{
        node 
		{
            label 'maven'
        }
    }
environment 
{
        SONAR_SCANNER_HOME = tool 'mfkimbell-sonar-scanner'
        JAVA_HOME = tool 'maven3.9.2' // Replace 'your-java-tool-name' with the name of the Java tool in Jenkins
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
}
    stages 
	{
        stage("build")
		{
            steps 
			{
                 echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                 echo "----------- build complted ----------"
            }
        }
		stage('SonarQube analysis') 
		{
			environment 
			{
				scannerHome = tool 'mfkimbell-sonar-scanner';
			}
			steps
			{
				withSonarQubeEnv('mfkimbell-sonarqube-server') 
				{ 
					sh "${scannerHome}/bin/sonar-scanner"	
				}
			
            }
        }
    }

}
