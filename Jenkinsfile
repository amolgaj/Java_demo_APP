pipeline {
    agent any

    environment {
        GIT_URL   = 'https://github.com/amolgaj/Java_demo_APP.git'
        GIT_CREDS = 'github-creds'
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
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Unit Test') {
            steps {
                echo "Running AppTest..."
                // Simulating test execution (replace with JUnit/Maven if needed)
                bat 'mvn test'
            }
        }

        stage('Package') {
            steps {
                echo "Packaging compiled classes..."
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Backup Current Deployment') {
            steps {
                echo "Backing up current deployment (if exists)..."
                bat '''
                if not exist C:\\deploy_demo mkdir C:\\deploy_demo
                if exist C:\\deploy_demo\\app.jar (
                    copy /Y C:\\deploy_demo\\app.jar C:\\deploy_demo\\app.jar.bak
                )
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying new shaded jar..."
                bat '''
                copy /Y target\\*-shaded.jar C:\\deploy_demo\\app.jar
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo "Verifying deployment..."
                bat 'java -jar C:\\deploy_demo\\app.jar'
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline executed successfully!"
        }
        failure {
            echo "❌ Something went wrong. Performing rollback..."
            bat '''
            if exist C:\\deploy_demo\\app.jar.bak (
                copy /Y C:\\deploy_demo\\app.jar.bak C:\\deploy_demo\\app.jar
            )
            '''
        }
    }
}
