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
                sh 'mvn clean deploy -Dmaven.test.skip=true' //skip the unit tests on build
                 echo "----------- build complted ----------"
            }
        }
		stage("test"){
            steps{
                echo "----------- unit test started ----------"
                sh 'mvn surefire-report:report'
                 echo "----------- unit test Complted ----------"
            }
        }

		// stage('SonarQube analysis') 
		// {
		// 	environment 
		// 	{
		// 		scannerHome = tool 'mfkimbell-sonar-scanner';
		// 	}
		// 	steps
		// 	{
		// 		withSonarQubeEnv('mfkimbell-sonarqube-server') 
		// 		{ 
		// 			sh "${scannerHome}/bin/sonar-scanner"	
		// 		}
			
        //     }
        // }
		// stage("Quality Gate")
		// {
		// 	steps 
		// 	{
		// 		script 
		// 		{
		// 			timeout(time: 1, unit: 'HOURS') 
		// 			{ // Just in case something goes wrong, pipeline will be killed after a timeout
		// 				def qg = waitForQualityGate() 
		// 				if (qg.status != 'OK') 
		// 				{
		// 					error "Pipeline aborted due to quality gate failure: ${qg.status}"
		// 				}
		// 			}
		// 		}
		// 	}
		// }
}
