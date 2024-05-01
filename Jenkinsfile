pipeline {
    agent {
        kubernetes {
            yamlFile 'jenkins-pod.yaml'
        }
    }

    stages {
        stage('Build') {
            steps {
                container('maven') {
                    // run a clean build without tests to see if the project compiles
                    sh 'mvn clean test-compile -DskipTests=true -Dmaven.javadoc.skip=true -B -V'
                }
            }
        }

        stage('Test') {
            steps {
                container('maven') {
                    // run JUnit tests
                    sh 'mvn test -B -V'
                }
            }
            post {
                // collect test results
                always {
                    junit 'target/surefire-reports/TEST-*.xml'
                    jacoco classPattern: 'target/classes,target/test-classes', execPattern: 'target/coverage-reports/*.exec', inclusionPattern: '**/*.class', sourcePattern: 'src/main/java,src/test/java'
                    archiveArtifacts 'target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.exec'
                }
            }
        }

        stage('Package') {
            steps {
                container('maven') {
                    sh 'mvn package -DskipTests=true -Dmaven.javadoc.skip=true -B -V'
                }
            }
            post {
                success {
                    archiveArtifacts 'target/*.jar'
                }
            }
        }

    }
}
