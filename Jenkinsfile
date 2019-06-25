pipeline {

    agent {
        label "master"
    }

    tools {
        maven "Maven 3.6.1"
    }

    environment {

        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "127.0.0.1:8081"
        NEXUS_REPOSITORY = "repository-testing"
        NEXUS_CREDENTIAL_ID = "nexus-credentials"

    }

    stages {

        stage ("Cloning the code") {

            steps {
                script {
                    git 'https://github.com/muheric/cargotracker.git'
                }
            }

        }

    }
    stage ("mvn publish") {
        steps {
            script {
                sh "mvn package -DskipTests=true"
            }
        }

    }

    stage ("publish to nexus") {
        steps {
            script {
                pom = readMavenPom file: "pom.xml";
                filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                artifactPath = filesByGlob[0].path;
                artifactExists = fileExists artifactPath;

                if (artifactExists) {

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

                                [artifactID: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],

                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]

                            ]
                    );
                }
                else {
                    error "*** File: ${artifactPath}, could not be found";
                }
            }

        }

    }



}