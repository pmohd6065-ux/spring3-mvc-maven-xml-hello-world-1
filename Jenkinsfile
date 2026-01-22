pipeline {
    agent any

    tools {
        maven 'MVN_HOME'
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
                    def currentVersion = sh(
                        script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout",
                        returnStdout: true
                    ).trim()

                    def parts = currentVersion.tokenize('.')
                    env.RELEASE_VERSION = "${parts[0]}.${(parts[1] as int) + 1}"

                    sh "mvn versions:set -DnewVersion=${env.RELEASE_VERSION} -DgenerateBackupPoms=false"
                }
            }
        }

        stage('Build & Test') {
            steps {
                sh 'java -version'
                sh 'mvn -version'
                sh 'mvn -B clean package'
            }
        }

        stage('Archive WAR') {
            steps {
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "✅ Build successful, version ${env.RELEASE_VERSION}"
        }
        failure {
            echo "❌ Build failed"
        }
    }
}
