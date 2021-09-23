pipeline {
    triggers {
        //githubPush()
        pollSCM('') //Empty quotes tells it to build on a push
    }

    agent {
        label 'master'
    }

    environment {
        M2_HOME = '3.5.4'
        JENKINS_VERSION = '2.190.2'
    }

    parameters {
        string(
                name: 'env',
                defaultValue: '',
                description: 'Sample Env variable'
        )
        string(
                name: 'branch',
                defaultValue: '',
                description: 'Please Provide branch You would like to build from'
        )
    }

    stages {
        stage('Initialize') {
            steps {
                catchError {
                    sh '''
                        echo "PATH=${PATH}"
                        echo "M2_HOME = ${M2_HOME}"
                    '''
                }
            }

            post {
                success {
                    echo 'Initialized Stage Successful . . .'
                }
                failure {
                    echo 'Initialize stage failed'
                    error('Stopping early…')

                }
            }
        }

        stage('Checkout SCM') {
            steps {
                step([$class: 'WsCleanup'])
                catchError {
                    echo 'Cloning..'
                    git branch: "${branch}" , credentialsId: 'github-ssh-key', url: 'http://localhost:7990/scm/test/testrepo.git'
                }
            }

            post {
                success {
                    echo 'Checkout SCM Stage Successful . . .'
                }
                failure {
                    echo 'Checkout SCM stage failed'
                    error('Stopping early…')
                }
            }
        }

        stage('Build'){
            steps{
                sh 'mkdir lib'
                sh 'cd lib/ ; wget https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.7.0/junit-platform-console-standalone-1.7.0-all.jar'
                sh 'cd src ; javac -cp "../lib/junit-platform-console-standalone-1.7.0-all.jar" CarTest.java Car.java App.java'
            }
        }

        stage('Test'){
            steps{
                sh 'cd src/ ; java -jar ../lib/junit-platform-console-standalone-1.7.0-all.jar -cp "." --select-class CarTest --reports-dir="reports"'
                junit 'src/reports/*-jupiter.xml'
            }
        }

        stage('Deploy'){
            steps{
                sh 'cd src/ ; java App'
            }
        }
    }
}
