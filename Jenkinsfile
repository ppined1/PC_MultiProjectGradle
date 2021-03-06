pipeline {
	//where and how to execute the Pipeline
	agent any
	
	options {
		buildDiscarder(logRotator(numToKeepStr: '5')) 
		disableConcurrentBuilds() 
	}
	
	//A section defining tools to auto-install and put on the PATH
	tools {
		jdk 'JDK8_Centos' //Preinstalada en la Configuración del Master
   	        gradle 'Gradle4.5_Centos' //Preinstalada en la Configuración del Master
	}
	
	triggers {
		pollSCM('@hourly')
	}
	
	stages{
		
		stage('Checkout') {
			steps{
				echo "------------>Checkout<------------"
				checkout([$class: 'GitSCM',
					  branches: [[name: 'master']],
					  doGenerateSubmoduleConfigurations: false, 
					  extensions: [],
					  gitTool: 'Git_Centos',
					  submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'GitHub_pedro_pineda', 
										 url: 'https://github.com/ppined1/PC_MultiProjectGradle']]])
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

		stage('Static Code Analysis') {
			steps{
				echo '------------>Análisis de código estático<------------'
				withSonarQubeEnv('Sonar') {
					sh "${tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'}/bin/sonar-scanner"
				}
			}
		}
		
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
		}
		always {
			echo 'This run was challenged'
		}
	}
}
