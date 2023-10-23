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
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        error("Pipeline failed: ${e.getMessage()}")
    }
}