pipeline{

    agent none
    stages{
        stage('worker/build'){
            agent{
                docker{
                    image 'maven:3.6.1-jdk-8-slim' 
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            when{
                changeset '**/worker/**'
            }
            steps{
                echo 'this is build step'
                dir('worker'){
                    sh 'mvn compile'
                } 
            }
        }
        stage('worker/test'){
            agent{
                docker{
                    image 'maven:3.6.1-jdk-8-slim' 
                    args '-v $HOME/.m2:/root/.m2'
                }
            }  
            when{
                changeset '**/worker/**'
            }         
            steps{
                echo 'this is a test step'
                dir('worker'){
                    sh 'mvn clean test'
                }                
            }
        }
        stage('worker/package'){
            agent{
                docker{
                    image 'maven:3.6.1-jdk-8-slim' 
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            when{
                changeset '**/worker/**'
            }           
            steps{
                echo 'this is a packeging.'
                dir('worker'){
                    sh 'mvn package -DskipTests'
                }
            }
        }
        stage('vote/build'){

            agent{
                docker{
                    image 'python:2.7.16-slim'
                    args '--user root'
                }   
            }
            when{
                changeset '**/vote/**'
            }            
            steps{
                dir('vote'){
                    sh 'pip install -r requirements.txt'
                }
            }
        }
        stage('vote/test'){
            agent{
                docker{
                    image 'python:2.7.16-slim'
                    args '--user root'
                }   
            } 
            when{
                changeset '**/vote/**'
            }            
            steps{
                dir('vote'){
                    sh 'pip install -r requirements.txt'
                    sh 'nosetests -v'
                }
            }
        }
        stage('vote/package'){
            agent any
            when{
                changeset '**/vote/**'
            } 
            steps{
                dir('vote'){
                    echo 'this step is package for vote'
                }
                
            }
        }

        stage('result/build'){
            agent any
            tools{
                nodejs 'NodeJs 16.2.0'
            }
            when{
                changeset '**/result/**'
            } 
            steps{
                echo 'build is running'
                dir('result'){
                    sh 'npm install'
                }
            
            }
        }

        stage('result/test'){
            agent any
            tools{
                nodejs 'NodeJs 16.2.0'
            }
            when{
                changeset '**/result/**'
            } 
            steps{
                echo 'test is running'
                dir('result'){
                    sh 'npm install'
                    sh 'npm test'
                }
            
            }
        }

        stage('Sonarqube') {
            agent any
            tools {
                jdk "JDK11" // the name you have given the JDK installation in Global Tool Configuration
            }

            environment{
                sonarpath = tool 'SonarScanner'
            }

            steps {
                echo 'Running Sonarqube Analysis..'
                withSonarQubeEnv('sonar-instavote') {
                sh "${sonarpath}/bin/sonar-scanner -Dproject.settings=sonar-project.properties -Dorg.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL=86400"
                }
            }
        }


        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                // true = set pipeline to UNSTABLE, false = don't
                waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('ıntegration test'){
            agent any
            steps{
                dir('vote'){
                  sh './integration_test.sh'  
                }
                
                
            }
        }
        stage('deploy'){
            agent any
            steps{
                echo 'compose running'
                sh 'docker-compose down'
                sh 'docker-compose build'
                sh 'docker-compose up -d'
            }
        }
    }

}
