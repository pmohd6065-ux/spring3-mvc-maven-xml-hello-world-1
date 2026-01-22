pipeline {
    agent any

    tools {
        maven 'MVN_HOME'
    }

    environment {
        NEXUS_VERSION       = 'nexus3'
        NEXUS_PROTOCOL      = 'http'
        NEXUS_URL           = '54.221.117.254:8081'
        NEXUS_REPOSITORY    = 'devops'
        NEXUS_CREDENTIAL_ID = 'Nexus'
        GROUP_ID            = 'com.ncodeit'
        ARTIFACT_ID         = 'ncodeit-hello-world'
    }

    stages {

        stage('Clone code') {
            steps {
                git 'https://github.com/pmohd6065-ux/spring3-mvc-maven-xml-hello-world-1.git'
            }
        }

        stage('Maven build') {
            steps {
                sh 'mvn -B clean package'
            }
        }

        stage('Read project version (plugin-safe)') {
            steps {
                script {
                    env.PROJECT_VERSION = sh(
                        script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout",
                        returnStdout: true
                    ).trim()

                    echo "Project version: ${env.PROJECT_VERSION}"
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                script {
                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,

                        groupId: GROUP_ID,
                        artifactId: ARTIFACT_ID,
                        version: PROJECT_VERSION,

                        artifacts: [[
                            classifier: '',
                            type: 'war',
                            file: "target/${ARTIFACT_ID}-${PROJECT_VERSION}.war"
                        ]]
                    )
                }
            }
        }
    }

    post {
        success {
            echo "✅ Uploaded ${ARTIFACT_ID}-${PROJECT_VERSION}.war to Nexus"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
