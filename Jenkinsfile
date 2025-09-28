pipeline {
    agent any

    environment {
        // Unique folder for each build
        REPORT_DIR = "${WORKSPACE}\\JMeter_Report_${BUILD_NUMBER}"
        ZIP_FILE   = "${WORKSPACE}\\JMeter_Report_${BUILD_NUMBER}.zip"
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
                REM === Create report folder ===
                mkdir "${REPORT_DIR}"

                REM === Run JMeter test ===
                call "E:\\Perf_Test\\Performance_Testing_KTDocument\\Performance_Testing_KTDocument\\Perf_training\\apache-jmeter-5.6.3\\bin\\jmeter.bat" ^
                 -n -t "${WORKSPACE}\\JMeterScripts\\Blazedemo_Script_4Dec.jmx" ^
                 -l "${WORKSPACE}\\jenkins_test_${BUILD_NUMBER}.csv" ^
                 -e -o "${REPORT_DIR}"
                """
            }
        }

        stage('Archive Report') {
            steps {
                echo "Archiving JMeter report folder"
                archiveArtifacts artifacts: "JMeter_Report_${BUILD_NUMBER}\\**", fingerprint: true
            }
        }

        stage('Publish HTML Report') {
            steps {
                echo "Publishing JMeter HTML report"
                publishHTML(target: [
                    reportName: "JMeter Performance Report",
                    reportDir: "${REPORT_DIR}",
                    reportFiles: 'index.html',
                    alwaysLinkToLastBuild: true,
                    keepAll: true
                ])
            }
        }

        stage('Cleanup Old Reports') {
            steps {
                echo "Cleaning up old reports except the current build"
                bat """
                powershell -command "Get-ChildItem -Path '${WORKSPACE}' -Directory | Where-Object { \$_.Name -like 'JMeter_Report_*' -and \$_.Name -ne 'JMeter_Report_${BUILD_NUMBER}' } | Remove-Item -Recurse -Force"
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! Report folder: ${REPORT_DIR}"
        }
        failure {
            echo "Pipeline failed. Check console output for errors."
        }
    }
}
