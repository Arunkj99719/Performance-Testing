pipeline {
    agent any

    environment {
        // Workspace folder for JMeter HTML report
        REPORT_DIR = "${WORKSPACE}\\JMeter_Report_${BUILD_NUMBER}"
        CSV_FILE   = "${WORKSPACE}\\jenkins_test_${BUILD_NUMBER}.csv"
        ZIP_FILE   = "${WORKSPACE}\\Jenkins_test_report_${BUILD_NUMBER}.zip"
        OUTPUT_DIR = "${WORKSPACE}\\Old_Reports"
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
                REM === Create report folder in workspace ===
                mkdir "${env.REPORT_DIR}"

                REM === Run JMeter test ===
                "E:\\Perf_Test\\Performance_Testing_KTDocument\\Perf_training\\apache-jmeter-5.6.3\\bin\\jmeter.bat" ^
                 -n -t "E:\\Perf_Test\\Performance_Testing_KTDocument\\Perf_training\\apache-jmeter-5.6.3\\bin\\Blazedemo_Script_4Dec.jmx" ^
                 -l "${env.CSV_FILE}" ^
                 -e -o "${env.REPORT_DIR}"
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
                    reportDir: "${env.REPORT_DIR}",
                    reportFiles: 'index.html',
                    reportName: "JMeter Performance Report (Build ${BUILD_NUMBER})"
                ])
            }
        }

        stage('Archive Report') {
            steps {
                echo "Zipping and archiving report folder"
                bat """
                powershell -command "Compress-Archive -Path '${env.REPORT_DIR}\\*' -DestinationPath '${env.ZIP_FILE}' -Force"
                """
                archiveArtifacts artifacts: "Jenkins_test_report_${BUILD_NUMBER}.zip", fingerprint: true
            }
        }

        stage('Cleanup Old Reports') {
            steps {
                echo "Cleaning up old report folders in workspace, keeping only the latest"
                bat """
                powershell -command "Get-ChildItem -Path '${WORKSPACE}' -Directory | Where-Object { \$_.Name -like 'JMeter_Report_*' -and \$_.Name -ne 'JMeter_Report_${BUILD_NUMBER}' } | Remove-Item -Recurse -Force"
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! HTML report is viewable in Jenkins and archived as ${ZIP_FILE}"
        }
        failure {
            echo 'Pipeline failed. Check console output for errors.'
        }
    }
}
