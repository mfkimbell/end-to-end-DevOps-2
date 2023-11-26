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
    PATH = "/opt/apache-maven-3.9.5/bin:$PATH"
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