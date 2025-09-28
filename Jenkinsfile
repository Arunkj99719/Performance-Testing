pipeline {
    agent any

    environment {
        JMETER_BIN  = "E:\\Perf_Test\\Performance_Testing_KTDocument\\Performance_Testing_KTDocument\\Perf_training\\apache-jmeter-5.6.3\\bin\\jmeter.bat"
        JMX_PATH    = "${WORKSPACE}\\JMeterScripts\\Blazedemo_Script_4Dec.jmx"
        REPORT_DIR  = "${WORKSPACE}\\JMeter_Report_${BUILD_NUMBER}"
        CSV_FILE    = "${WORKSPACE}\\jenkins_test_${BUILD_NUMBER}.csv"
        ZIP_FILE    = "${WORKSPACE}\\JMeter_Report_${BUILD_NUMBER}.zip"
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
                echo "Running JMeter script: ${JMX_PATH}"
                bat """
                REM === Create report folder in workspace ===
                mkdir "${REPORT_DIR}"

                REM === Run JMeter test ===
                "${JMETER_BIN}" -n -t "${JMX_PATH}" -l "${CSV_FILE}" -e -o "${REPORT_DIR}"
                """
            }
        }

        stage('Archive Report') {
            steps {
                echo "Zipping JMeter report folder"
                bat """
                powershell -command "Compress-Archive -Path '${REPORT_DIR}\\*' -DestinationPath '${ZIP_FILE}' -Force"
                """
                archiveArtifacts artifacts: "JMeter_Report_${BUILD_NUMBER}.zip", fingerprint: true
            }
        }

        stage('Publish HTML Report') {
            steps {
                echo "Publishing JMeter HTML report"
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

        stage('Cleanup Old Reports') {
            steps {
                echo "Deleting old report folders, keeping only current build"
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
