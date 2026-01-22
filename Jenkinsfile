pipeline {
    agent any

    tools {
        maven 'M3'     // must exist in Jenkins tools
        jdk 'JDK17'    // or JDK8 if your infra requires it
    }

    environment {
        NEXUS_REPO = 'nexus-releases'
        NEXUS_URL  = 'http://nexus.company.com:8081'
        GROUP_ID   = 'com.ncodeit'
        ARTIFACT_ID = 'ncodeit-hello-world'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -B clean package'
            }
        }

        stage('Read Version (plugin-safe)') {
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

        stage('Publish to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-creds',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {

                    sh """
                        mvn deploy -B \
                          -DskipTests \
                          -Dnexus.url=${NEXUS_URL} \
                          -DaltDeploymentRepository=${NEXUS_REPO}::default::${NEXUS_URL}/repository/${NEXUS_REPO} \
                          -Dnexus.username=$NEXUS_USER \
                          -Dnexus.password=$NEXUS_PASS
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build & publish successful: ${ARTIFACT_ID}-${PROJECT_VERSION}.war"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
