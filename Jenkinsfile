stage('Package Report') {
    steps {
        echo "Zipping JMeter report folder"
        bat """
        powershell -command "Compress-Archive -Path '${env.REPORT_DIR}\\*' -DestinationPath '${WORKSPACE}\\Jenkins_test_report_${BUILD_NUMBER}.zip' -Force"
        """
    }
}

stage('Publish Report') {
    steps {
        echo "Publishing JMeter HTML report"

        // Archive the zip inside workspace
        archiveArtifacts artifacts: "Jenkins_test_report_${BUILD_NUMBER}.zip", fingerprint: true

        // Publish HTML directly from report dir
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
