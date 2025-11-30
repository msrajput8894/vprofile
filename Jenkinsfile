pipeline{
    agent any
    triggers{
        githubPush()
    }
    tools {
        maven 'MAVEN3.9'
        jdk 'JDK17'
    }

    stages{
        stage('Fetch Code') {
            steps {
                git branch: 'main', url: 'https://github.com/msrajput8894/vprofile.git'
            }
        }
        stage('Unit Test'){
            steps{
                sh 'mvn test'
            }
        }
        stage('Build Project'){
            steps{
                sh 'mvn install -DskipTests'
            }
            post {
                success {
                    echo 'Archiving the build artifacts...'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Checkstyle Analysis'){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('SonarQube Analysis'){
            environment {
                scannerHome = tool 'sonar6.2'
            }
            steps{
                withSonarQubeEnv('sonarserver'){
                    sh '''${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=vprofile \
                    -Dsonar.projectName=VProfile \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportPaths=target/surefire-reports \
                    -Dsonar.jacoco.reportPaths=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }
        stage('Quality Gate'){
            steps{
                timeout(time: 1, unit: 'HOURS'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}