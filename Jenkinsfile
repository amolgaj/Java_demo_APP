pipeline {
    agent any

    environment {
        GIT_URL   = 'https://github.com/amolgaj/Java_demo_APP.git'
        GIT_CREDS: 'github-creds'0
        DEPLOY_DIR = 'C:/deploy_demo'
    }

    stages {

        stage('Checkout') {
            steps {
               echo "Pulling code from GitHub..."
                git branch: 'main',
                    url: "${GIT_URL}",
                    credentialsId: "${GIT_CREDS}"
            }
        }

        stage('Build') {
            steps {
                echo "Compiling App.java and AppTest.java..."
                sh 'javac -d build App.java AppTest.java'
            }
        }

        stage('Unit Test') {
            steps {
                echo "Running AppTest..."
                // Simulating test execution (replace with JUnit/Maven if needed)
                sh 'java -cp build AppTest'
            }
        }

        stage('Package') {
            steps {
                echo "Packaging compiled classes..."
                sh 'jar cvf build/app.jar -C build .'
                archiveArtifacts artifacts: 'build/app.jar', fingerprint: true
            }
        }

        stage('Backup Current Deployment') {
            steps {
                echo "Backing up current deployment (if exists)..."
                sh """
                    mkdir -p "${DEPLOY_DIR}"
                    if [ -f "${DEPLOY_DIR}/app.jar" ]; then
                        cp "${DEPLOY_DIR}/app.jar" "${DEPLOY_DIR}/app.jar.bak"
                    fi
                """
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying new version..."
                sh 'cp build/app.jar "${DEPLOY_DIR}/app.jar"'
            }
        }

        stage('Verify Deployment') {
            steps {
                echo "Verifying deployment..."
                script {
                    def deployed = fileExists("${DEPLOY_DIR}/app.jar")
                    if (!deployed) {
                        error("Deployment verification failed!")
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline executed successfully!"
        }
        failure {
            echo "❌ Something went wrong. Performing rollback..."
            script {
                sh """
                    if [ -f "${DEPLOY_DIR}/app.jar.bak" ]; then
                        cp "${DEPLOY_DIR}/app.jar.bak" "${DEPLOY_DIR}/app.jar"
                        echo "Rollback completed: previous version restored."
                    else
                        echo "No backup found. Rollback not possible."
                    fi
                """
            }
        }
    }
}
