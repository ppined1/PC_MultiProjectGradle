pipeline {
	//where and how to execute the Pipeline
	agent any
	
	options {
		buildDiscarder(logRotator(numToKeepStr: '5')) 
		disableConcurrentBuilds() 
	}
	
	//A section defining tools to auto-install and put on the PATH
	tools {
		jdk 'JDK8'
		gradle 'Gradle4.5'
	}
	
	triggers {
		pollSCM('@hourly')
	}
	
	stages{
		
		stage('Checkout') {
			steps{
				echo "------------>Checkout<------------"
				checkout([$class: 'GitSCM', branches: [[name: 'master']], doGenerateSubmoduleConfigurations: false, extensions: [], gitTool: 'Git_Centos', submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'GitHub_yucaci24', url: 'https://github.com/yucaci24/PC_MultiProjectGradle']]])
				sh 'gradle clean'
			}
		}
		
		stage('Unit Tests') {
			steps{
				echo "------------>Unit Tests<------------"
				sh 'gradle test'
				junit '**/build/test-results/test/*.xml' //aggregate test results - JUnit
			}
		}

		/*stage('Static Code Analysis') {
			steps{
				echo '------------>Análisis de código estático<------------'
				withSonarQubeEnv('Sonar') {
					sh "${tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'}/bin/sonar-scanner"
				}
			}
		}*/
		
		stage('Build') {
			steps {
				echo "------------>Build<------------"
				sh 'gradle build -x test'
			}
		}
	}
	
	post {
		failure {
			echo 'This will run only if failed'
			//send notifications about a Pipeline to an email
			mail (to: 'yuliana.canas@ceiba.com.co',
			      subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
			      body: "Something is wrong with ${env.BUILD_URL}")
		}
		success {
			submitJUnitTestResultsToqTest([apiKey: 'e9da9c87-edfd-4dc3-bdf5-71685d6122df', containerID: 7, containerType: 'release', createNewTestRunsEveryBuildDate: false, createTestCaseForEachJUnitTestClass: true, createTestCaseForEachJUnitTestMethod: false, overwriteExistingTestSteps: true, parseTestResultsFromTestingTools: false, projectID: 2, qtestURL: 'https://qtest-demo.ceiba.com.co', submitToAReleaseAsSettingFromQtest: false, submitToExistingContainer: true, utilizeTestResultsFromCITool: true])
		}
	}
}
