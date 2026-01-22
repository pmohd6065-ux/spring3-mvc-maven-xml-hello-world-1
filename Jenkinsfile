pipeline {
    agent any

    tools {
        maven 'MVN_HOME'
        jdk 'JDK17'
    }

    environment {
        NEXUS_VERSION       = 'nexus3'
        NEXUS_PROTOCOL      = 'http'
        NEXUS_URL           = '54.221.117.254:8081'
        NEXUS_REPOSITORY    = 'RELEASE'
        NEXUS_CREDENTIAL_ID = 'Nexus'

        GROUP_ID    = 'com.ncodeit'
        ARTIFACT_ID = 'ncodeit-hello-world'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Read & bump version') {
            steps {
                script {
                    // Read current version from pom.xml
                    def currentVersion = sh(
                        script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout",
                        returnStdout: true
                    ).trim()

                    echo "Current version: ${currentVersion}"

                    // Split MAJOR.MINOR
                    def parts = currentVersion.tokenize('.')
                    def major = parts[0]
                    def minor = parts[1] as int

                    // Auto-increment MINOR
                    def nextVersion = "${major}.${minor + 1}"

                    env.RELEASE_VERSION = nextVersion

                    echo "Next release version: ${env.RELEASE_VERSION}"

                    // Update pom.xml
                    sh "mvn versions:set -DnewVersion=${env.RELEASE_VERSION} -DgenerateBackupPoms=false"
                }
            }
        }

        stage('Build release') {
            steps {
                sh 'mvn -B clean package'
            }
        }

        stage('Upload to Nexus (RELEASE)') {
            steps {
                script {
                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,

                        groupId: GROUP_ID,
                        version: RELEASE_VERSION,

                        artifacts: [[
                            artifactId: ARTIFACT_ID,
                            type: 'war',
                            file: "target/${ARTIFACT_ID}-${RELEASE_VERSION}.war"
                        ]]
                    )
                }
            }
        }

        stage('Commit version bump') {
            steps {
                sh """
                   git status
                   git commit -am "Release ${RELEASE_VERSION}"
                   git push origin HEAD:main
                """
            }
        }
    }

    post {
        success {
            echo "✅ Released ${ARTIFACT_ID}-${RELEASE_VERSION}"
        }
        failure {
            echo "❌ Release failed"
        }
    }
}
