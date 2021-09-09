pipeline {

    agent any

    stages {
        stage("build") {
            steps {
                echo "building the application..."
                sh """
                    /opt/Qt5.14.2/5.14.2/gcc_64/bin/qmake TestApp.pro
                    make clean
                    make -j4
                """
            }
        }

        stage("cia") {
            steps {
                echo "cia running..."
                sh """
                    pwd
                    sh ~/.jenkins/cia/clone_versions.sh
                    cd ~/.jenkins/cia/
                    sh cia.sh
                """
            }
        }

        stage("ut") {
            steps {
                echo "unit testing..."
                sh """
                    cd ~/.jenkins
                    java -jar helloworld.jar
                """
            }
        }
    }

    post {
         always {
             echo 'There is a push to TestApp project.'
         }
         success {
             echo 'This will run only if successful'
             emailext attachmentsPattern: '../cia_report/output.html',
                body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                recipientProviders: [developers(), requestor()],
                subject: "SUCCESS CI: Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}", 
                to: "hoannv41@gmail.com";
         }
         failure {
             emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                recipientProviders: [developers(), requestor()],
                subject: "ERROR CI: Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}", 
                to: "hoannv41@gmail.com";
         }
         unstable {
             echo 'This will run only if the run was marked as unstable'
         }
         changed {
             echo 'This will run only if the state of the Pipeline has changed'
             echo 'For example, if the Pipeline was previously failing but is now successful'
         }
     }
}
