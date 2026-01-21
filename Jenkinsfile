pipeline {
    agent any 
    tools {
        // Note: this should match with the tool name configured in your jenkins instance (JENKINS_URL/configureTools/)
        sh "MVN_HOME"
        
    }
	 environment {
        // This can be nexus3 or nexus2pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "3.133.145.136:8081"
        NEXUS_REPOSITORY = "devops"
        NEXUS_CREDENTIAL_ID = "Nexus_server"
    }

    stages {
        stage("Clone Code") {
            steps {
                git 'https://github.com/betawins/spring3-mvc-maven-xml-hello-world-1.git'
            }
        }

        stage("Maven Build") {
            steps {
                sh 'mvn -Dmaven.test.skip=true clean package'
            }
        }

        stage("Publish to Nexus") {
            steps {
                script {
                    def pom = readMavenPom file: 'pom.xml'
                    def files = findFiles(glob: "target/*.${pom.packaging}")

                    if (files.length == 0) {
                        error "No artifact found"
                    }

                    def artifactPath = files[0].path

                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        groupId: pom.groupId,
                        artifactId: pom.artifactId,
                        version: "${BUILD_NUMBER}",
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,
                        artifacts: [
                            [
                                artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging
                            ],
                            [
                                artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"
                            ]
                        ]
                    )
                }
            }
        }
    }
}

        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "3.133.145.136:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "devops"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "Nexus_server"
    }
    stages {
        stage("clone code") {
            steps {
                script {
                    // Let's clone the source
                    git 'https://github.com/betawins/spring3-mvc-maven-xml-hello-world-1.git';
                }
            }
        }
        stage("mvn build") {
            steps {
                script {
                    // If you are using Windows then you should use "bat" step
                    // Since unit testing is out of the scope we skip them
                    sh 'mvn -Dmaven.test.failure.ignore=true install'
                }
            }
        }
        stage("publish to nexus") {
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version $BUILD_NUMBER}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
			    groupId: pom.groupId,
                            version: '${BUILD_NUMBER}',
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                // Lets upload the pom.xml file for additional information for Transitive dependencies
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
