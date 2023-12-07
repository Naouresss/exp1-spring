/*pipeline {
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
*/
pipeline {
    agent any 
    tools { 
        maven 'maven'
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "10.20.2.251:8081"
        NEXUS_REPOSITORY = "maven-nexus-repo"
        NEXUS_CREDENTIAL_ID = "nexus-user-credentials"
    }
    stages {
        stage ("Clean up"){
            steps {
                deleteDir()
            }
        }
        stage ("Clone repo"){
            steps {
                sh "git clone https://github.com/Naouresss/exp1-spring.git"
            }
        }
        stage('Build') {
            steps {
                dir("exp1-spring"){
                      sh "mvn clean install"
                      sh "docker build -t sona-nexus-app ."

                  }
            }
        }

        stage ("Run docker compose") {
            steps {
                 dir("exp1-spring"){
                    sh " docker compose up -d"
                }                
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    dir("exp1-spring"){
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        stage("Publish to Nexus Repository Manager") {
            steps {
                 dir("exp1-spring"){

                     script {
                        pom = readMavenPom file: "pom.xml";
                        filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                        echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                        artifactPath = filesByGlob[0].path;
                        artifactExists = fileExists artifactPath;
                        if(artifactExists) {
                            echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                            nexusArtifactUploader(
                                nexusVersion: NEXUS_VERSION,
                                protocol: NEXUS_PROTOCOL,
                                nexusUrl: NEXUS_URL,
                                groupId: pom.groupId,
                                version: pom.version,
                                repository: NEXUS_REPOSITORY,
                                credentialsId: NEXUS_CREDENTIAL_ID,
                                artifacts: [
                                    [artifactId: pom.artifactId,
                                    classifier: '',
                                    file: artifactPath,
                                    type: pom.packaging],
                                    [artifactId: pom.artifactId,
                                    classifier: '',
                                    file: "pom.xml",
                                    type: "pom"]
                                ]
                            );
                        } else {
                        error "*** File: ${artifactPath}, could not be found";
                        }
                    }    
                }
            }
        }
         
        
    }
}
