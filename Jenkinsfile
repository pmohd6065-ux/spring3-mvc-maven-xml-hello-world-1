pipeline {
    agent any

    tools {
        maven "MVN_HOME"
    }

    environment {
        NEXUS_VERSION       = "nexus3"
        NEXUS_PROTOCOL      = "http"
        NEXUS_URL           = "54.221.117.254:8081"
        NEXUS_REPOSITORY    = "devops"
        NEXUS_CREDENTIAL_ID = "nexus"
    }

    stages {
        stage("clone code") {
            steps {
                git 'https://github.com/pmohd6065-ux/spring3-mvc-maven-xml-hello-world-1.git'
            }
        }

        stage("mvn build") {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean install'
            }
        }

        stage("publish to nexus") {
            steps {
                script {
                    def pom = readMavenPom file: "pom.xml"

                    def filesByGlob = findFiles(glob: "target/*.${pom.packaging}")
                    if (filesByGlob.length == 0) {
                        error "No artifact found in target directory"
                    }

                    def artifactPath = filesByGlob[0].path

                    echo "*** Uploading ${artifactPath}"
                    echo "*** GroupId: ${pom.groupId}"
                    echo "*** ArtifactId: ${pom.artifactId}"
                    echo "*** Packaging: ${pom.packaging}"
                    echo "*** Version: ${BUILD_NUMBER}"

                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        groupId: pom.groupId,
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
