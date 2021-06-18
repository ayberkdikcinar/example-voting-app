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
            steps{
                dir('vote'){
                    sh 'pip install -r requirements.txt'
                    sh 'nosetests -v'
                }
            }
        }
        stage('vote/package'){
            agent any
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
            steps{
                echo 'test is running'
                dir('result'){
                    sh 'npm install'
                    sh 'npm test'
                }
            
            }
        }
        stage('deploy'){
            agent any
            steps{
                echo 'compose running'
                sh 'docker-compose up -d'
            }
        }
    }

}
