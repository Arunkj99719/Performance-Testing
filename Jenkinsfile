pipeline {
    agent any

    environment {
        // Unique folder for each build using timestamp
        REPORT_DIR = "E:\\Perf_Test\\RAW_FILES\\OUTPUT\\Jenkins_test_report_${new Date().format('yyyyMMdd_HHmmss')}"
        ZIP_FILE   = "${WORKSPACE}\\Jenkins_test_report_${BUILD_NUMBER}.zip"
        OUTPUT_DIR = "E:\\Perf_Test\\RAW_FILES\\OUTPUT"
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

        stage('Package Report') {
            steps {
                echo "Zipping JMeter report folder"
                bat """
                powershell -command "Compress-Archive -Path '${env.REPORT_DIR}\\*' -DestinationPath '${env.ZIP_FILE}' -Force"
                """
            }
        }

        stage('Cleanup Old Reports') {
            steps {
                echo "Deleting old report folders in ${env.OUTPUT_DIR}, keeping only the current build"
                bat """
                powershell -command "Get-ChildItem -Path '${env.OUTPUT_DIR}' -Directory | Where-Object { \$_.Name -like 'Jenkins_test_report_*' -and \$_.FullName -ne '${env.REPORT_DIR}' } | Remove-Item -Recurse -Force"
                """
            }
        }

        stage('Publish Report') {
            steps {
                echo "Publishing JMeter HTML report"

                // Archive the zip inside workspace
                archiveArtifacts artifacts: "Jenkins_test_report_${BUILD_NUMBER}.zip", fingerprint: true

                // Publish HTML for quick preview
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: "${env.REPORT_DIR}",
                    reportFiles: 'index.html',
                    reportName: "JMeter Performance Report (Build ${BUILD_NUMBER})"
                ])
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! Report archived at: ${ZIP_FILE}"
        }
        failure {
            echo 'Pipeline failed. Check console output for errors.'
        }
    }
}
