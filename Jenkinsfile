pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Spring Boot') {
            steps {
                sh 'bash ./mvnw -B clean package -DskipTests'
            }
        }

        stage('Archive JAR') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-credentials',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh '''
                        curl --fail --show-error --silent \
                          --user "$NEXUS_USER:$NEXUS_PASS" \
                          --upload-file target/spring-boot-complete-1.0.0.jar \
                          http://nexus:8081/repository/hw8-releases/com/example/spring-boot-complete/1.0.0/spring-boot-complete-1.0.0.jar
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Build and Nexus upload successful!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
