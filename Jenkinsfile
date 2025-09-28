pipeline {
    agent any

    environment {
        // Unique folder for each build using timestamp
        REPORT_DIR = "E:\\Perf_Test\\RAW_FILES\\OUTPUT\\Jenkins_test_report_${new Date().format('yyyyMMdd_HHmmss')}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out Sample_Script branch from GitHub"
                git branch: 'Sample_Script', url: 'https://github.com/Arunkj99719/Performance-Testing.git'
            }
        }

        stage('Run JMeter') {
            steps {
                echo "Running JMeter script: Blazedemo_Script_4Dec.jmx"
                bat """
                REM === Create unique report folder ===
                mkdir "${env.REPORT_DIR}"

                REM === Run JMeter test ===
                "E:\\Perf_Test\\Performance_Testing_KTDocument\\Performance_Testing_KTDocument\\Perf_training\\apache-jmeter-5.6.3\\bin\\jmeter.bat" ^
                 -n -t "E:\\Perf_Test\\Performance_Testing_KTDocument\\Performance_Testing_KTDocument\\Perf_training\\apache-jmeter-5.6.3\\bin\\Blazedemo_Script_4Dec.jmx" ^
                 -l "E:\\Perf_Test\\RAW_FILES\\OUTPUT\\jenkins_test_${BUILD_NUMBER}.csv" ^
                 -e -o "${env.REPORT_DIR}"
                """
            }
        }

        stage('Publish Report') {
            steps {
                echo "Publishing JMeter HTML report from ${env.REPORT_DIR}"
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: "${env.REPORT_DIR}",
                    reportFiles: 'index.html',
                    reportName: "JMeter Performance Report (Build ${BUILD_NUMBER})",
                    includeResources: true
                ])
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! HTML report published at: ${env.REPORT_DIR}"
        }
        failure {
            echo 'Pipeline failed. Check console output for errors.'
        }
    }
}
