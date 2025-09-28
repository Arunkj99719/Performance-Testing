pipeline {
    agent any

    environment {
        // Update this path if your JMeter installation is elsewhere
        JMETER_BIN   = "E:\\Perf_Test\\Perf_JMeter\\apache-jmeter-5.6.3\\bin\\jmeter.bat"
        JMETER_SCRIPT = "${WORKSPACE}\\JMeterScripts\\Blazedemo_Script_4Dec.jmx"

        // Workspace-based unique report folder for each build
        REPORT_DIR   = "${WORKSPACE}\\JMeter_Report_${BUILD_NUMBER}"
        CSV_FILE     = "${WORKSPACE}\\jenkins_test_${BUILD_NUMBER}.csv"
        ZIP_FILE     = "${WORKSPACE}\\JMeter_Report_${BUILD_NUMBER}.zip"
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
                echo "Running JMeter script: ${JMETER_SCRIPT}"
                bat """
                REM === Create report folder in workspace ===
                mkdir "${REPORT_DIR}"

                REM === Run JMeter test ===
                "${JMETER_BIN}" -n -t "${JMETER_SCRIPT}" -l "${CSV_FILE}" -e -o "${REPORT_DIR}"
                """
            }
        }

        stage('Publish HTML Report') {
            steps {
                echo "Publishing JMeter HTML report inside Jenkins"
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: "${REPORT_DIR}",
                    reportFiles: 'index.html',
                    reportName: "JMeter Performance Report (Build ${BUILD_NUMBER})"
                ])
            }
        }

        stage('Archive Report') {
            steps {
                echo "Zipping JMeter report for download"
                bat """
                powershell -command "Compress-Archive -Path '${REPORT_DIR}\\*' -DestinationPath '${ZIP_FILE}' -Force"
                """
                archiveArtifacts artifacts: "JMeter_Report_${BUILD_NUMBER}.zip", fingerprint: true
            }
        }

        stage('Cleanup Old Reports') {
            steps {
                echo "Deleting old report folders in workspace, keeping only the current build"
                bat """
                powershell -command "Get-ChildItem -Path '${WORKSPACE}' -Directory | Where-Object { \$_.Name -like 'JMeter_Report_*' -and \$_.FullName -ne '${REPORT_DIR}' } | Remove-Item -Recurse -Force"
                """
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
