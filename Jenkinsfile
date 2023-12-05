pipeline {
    agent any 
    tools { 
        maven 'maven'
    }
    stages {
        stage("Clean up") {
            steps {
                deleteDir()
            }
        }
        stage("Clone repo") {
            steps {
                sh "git clone https://github.com/Naouresss/exp1-spring.git"
            }
        }
        // Uncomment and adjust if you want to include these stages
        /*
        stage("Generate backend image") {
            steps {
                dir("exp1-spring") {
                    sh "mvn clean install"
                    sh "docker build -t docexp1-spring ."
                }
            }
        }

        stage("Run docker compose") {
            steps {
                dir("exp1-spring") {
                    sh "docker-compose up -d"
                }
            }
        }
        */
        stage("Build") {
            steps {
                dir("exp1-spring") {
                    sh "mvn clean install"
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    dir("exp1-spring") {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }
    }
}
