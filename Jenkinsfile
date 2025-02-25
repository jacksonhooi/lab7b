//lab 7b
// pipeline {
// 	agent none
	
// 	stages {
		
// 		stage('Integration UI Test') {
// 			parallel {
// 				stage('Deploy') {
// 					agent any
// 					steps {
// 						sh './jenkins/scripts/deploy.sh'
// 						input message: 'Finished using the web site? (Click "Proceed" to continue)'
// 						sh './jenkins/scripts/kill.sh'
// 					}
// 				}
// 				stage('Headless Browser Test') {
// 					agent {
// 						docker {
// 							image 'maven:3-alpine' 
// 							args '-v /root/.m2:/root/.m2' 
// 						}
// 					}
// 					steps {
// 						sh 'mvn -B -DskipTests clean package'
// 						sh 'mvn test'
// 					}
// 					post {
// 						always {
// 							junit 'target/surefire-reports/*.xml'
// 						}
// 					}
// 				}
// 			}
// 		}
		
// 	}
// }

//lab 8 
// pipeline {
//     agent any

//     stages {
//         stage('Checkout') {
//             steps {
//                 git branch: 'master', url: 'https://github.com/ScaleSec/vulnado.git'
//             }
//         }

//         stage('Build') {
//             steps {
//                 sh '/var/jenkins_home/apache-maven-3.6.3/bin/mvn --batch-mode -V -U -e clean verify -Dsurefire.useFile=false -Dmaven.test.failure.ignore'
//             }
//         }

//         stage('Analysis') {
//             steps {
//                 sh '/var/jenkins_home/apache-maven-3.6.3/bin/mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs'
//             }
//         }
//     }

//     post {
//         always {
//             junit testResults: '**/target/surefire-reports/TEST-*.xml'
//             recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc(), checkStyle()]
//             recordIssues enabledForFailure: true, tools: [spotBugs(pattern: '**/target/findbugsXml.xml')]
//             recordIssues enabledForFailure: true, tools: [cpd(pattern: '**/target/cpd.xml')]
//             recordIssues enabledForFailure: true, tools: [pmdParser(pattern: '**/target/pmd.xml')]
//         }
//     }
// }

// lab 9
// pipeline {
//     agent any
//     stages {
//         stage('Checkout') {
//             steps {
//                 git branch: 'master', url: 'https://github.com/jacksonhooi/lab7b.git'
//             }
//         }
//         stage('Code Quality Check via SonarQube') {
//             steps {
//                 script {
//                     def scannerHome = tool 'SonarQube'
//                     withSonarQubeEnv('SonarQube') {
//                         sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=lab7 -Dsonar.sources=."
//                     }
//                 }
//             }
//         }
//     }
//     post {
//         always {
//             recordIssues(enabledForFailure: true, tools: [sonarQube()])
//         }
//     }
// }


// combine
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/jacksonhooi/lab7b.git'
            }
        }

        stage('Code Quality Check via SonarQube') {
            steps {
                script {
                    def scannerHome = tool 'SonarQube'
                    withSonarQubeEnv('Testing') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Testing -Dsonar.sources=."
                    }
                }
            }
        }

        stage('OWASP Dependency-Check Vulnerabilities') {
            steps {
                dependencyCheck additionalArguments: ''' 
                    -o './'
                    -s './'
                    -f 'ALL' 
                    --prettyPrint''', odcInstallation: 'OWASP Dependency-Check Vulnerabilities'
            }
        }

        stage('Integration UI Test') {
            parallel {
                stage('Deploy') {
                    steps {
                        sh './jenkins/scripts/deploy.sh'
                        input message: 'Finished using the web site? (Click "Proceed" to continue)'
                        sh './jenkins/scripts/kill.sh'
                    }
                }

                stage('Headless Browser Test') {
                    agent {
                        docker {
                            image 'maven:3-alpine'
                            args '-v /root/.m2:/root/.m2'
                        }
                    }
                    steps {
                        sh 'mvn -B -DskipTests clean package'
                        sh 'mvn test'
                    }
                    post {
                        always {
                            junit 'target/surefire-reports/*.xml'
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            recordIssues(enabledForFailure: true, tools: [sonarQube()])
        }
        success {
            dependencyCheckPublisher pattern: 'dependency-check-report.xml'
        }
    }
}
//trying
