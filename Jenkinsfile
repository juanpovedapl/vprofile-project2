pipeline {
    agent any
    tools {
        maven "MAVEN3.9"
        jdk "SDK17"
    }
    
    environment {
        SNAP_REPO = 'vprofile-snapshot'
		NEXUS_USER = 'admin'
		NEXUS_PASS = 'Devil93%'
		RELEASE_REPO = 'vprofile-release'
		CENTRAL_REPO = 'vpro-maven-central'
		NEXUSIP = '172.31.23.136'
		NEXUSPORT = '8081'
		NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARSERVER='sonarserver'
        SONARSCANNER="sonarscanner"
    }

    stages {
        stage('Build'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post {
                success {
                    echo "Now Archiving"
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Test'){
            steps{
                sh 'mvn -s settings.xml test'
            }
        }
        stage('Checkstyle Analysis'){
            steps{
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }
        stage('Check SonarScanner version') {
            environment {
                SCANNER_HOME = tool "${SONARSCANNER}"
            }
            steps {
                sh 'echo "SonarScanner is installed at: $SCANNER_HOME xd"'
                sh '$SCANNER_HOME/bin/sonar-scanner -v'
            }
        }

        stage('Sonar Analysis'){
            environment{
                scannerHome = tool "${SONARSCANNER}"
            }
            steps{
                withSonarQubeEnv("${SONARSERVER}"){
                    sh '''export SONAR_SCANNER_OPTS="--add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.stream=ALL-UNNAMED" 
                    
                    ${scannerHome}/bin/sonar-scanner -X -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile  \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''

                }
            }
        }
    }
}