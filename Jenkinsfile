node {
    try {
        stage('Build') {
            def mavenImage = docker.image('maven:3.9.0')
            try {
                mavenImage.inside("-v /root/.m2:/root/.m2") {
                    sh 'mvn -B -DskipTests clean package'
                }
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                error("Build failed: ${e.getMessage()}")
            }
        }
        stage('Test') {
            def mavenImage = docker.image('maven:3.9.0')
            try {
                mavenImage.inside("-v /root/.m2:/root/.m2") {
                    sh 'mvn test'
                }
                junit 'target/surefire-reports/*.xml'
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                error("Test failed: ${e.getMessage()}")
            }
        }
        stage('Deliver') {
            def mavenImage = docker.image('maven:3.9.0')
            try {
                mavenImage.inside("-v /root/.m2:/root/.m2") {
                    sh './jenkins/scripts/deliver.sh'
                }
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                error("Deliver failed: ${e.getMessage()}")
            }
        }
        stage('Manual Approval') {
            def userInput = input(
                id: 'manual-approval',
                message: 'Lanjutkan ke tahap Deploy?',
                parameters: [choice(choices: 'Proceed\nAbort', description: 'Pilih untuk melanjutkan atau menghentikan')]
            )

            if (userInput == 'Abort') {
                error('Pipeline dihentikan oleh pengguna')
            }
        }
        stage('Deploy') {
            def mavenImage = docker.image('maven:3.9.0')
            try {
                mavenImage.inside("-v /root/.m2:/root/.m2") {
                    sh './jenkins/scripts/deliver.sh'
                }
                sleep time: 60, unit: 'SECONDS'
            } catch (Exception e) {
                currentBuild.result = 'FAILURE'
                error("Deploy failed: ${e.getMessage()}")
            }
        }
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        error("Pipeline failed: ${e.getMessage()}")
    }
}