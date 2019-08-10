pipeline {
	agent{
		label 'master'
	}
	tools {
		maven 'maven'
	}
	environment{ COMPLIANCEENABLED = true }
	options{
		skipDefaultCheckout()
		buildDiscarder(logRotator(artifactDaysToKeepStr: '1', artifactNumToKeepStr: '1', daysToKeepStr: '5', numToKeepStr: '10'))
	}
	stages{
		stage('Build'){
			steps{
				checkout scm
				sh 'mvn -DskipTests clean install'
				stash includes: 'target/*.jar', name: 'artifact'
			}
			post {

				success{ echo " Build stage completed" }
				failure{ echo " Build stage failed" }
			}
		}

		stage('Unit Test'){

			steps{
				checkout scm
				sh 'mvn test'
			}
			post {
				success{
					script{

						echo 'STARTING SUREFIRE TEST'
						sh 'mvn surefire-report:report-only'
					}

					echo " Unit Test stage completed"
				}
				failure{
					echo " Unit Test stage failed"
				}
			}
		}


		stage('Documentation'){

			steps{
				checkout scm
				sh 'mvn -e javadoc:javadoc'
			}
			post {
				always{ deleteDir() }
				success{
					script{

						step([$class: 'JavadocArchiver', javadocDir:'target/site/apidocs', keepAll:false])
					}

					echo " Documentation stage completed"
					deleteDir()
				}
				failure{
					echo " Documentation stage failed"
					deleteDir()
				}
			}
		}
	}
}

