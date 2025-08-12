pipeline {
    agent any

    environment {
        // Centralize Tomcat path for easier edits
        TOMCAT_HOME = 'C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1'
        FRONTEND_DIST = 'STUDENTAPI-REACT\\dist'
        BACKEND_WAR = 'STUDENTAPI-SPRINGBOOT\\target'
    }

    stages {
        // ===== FRONTEND BUILD ===== //
        stage('Build Frontend') {
            steps {
                dir('STUDENTAPI-REACT') {
                    bat 'npm ci' // 'npm ci' is preferable for CI environments
                    bat 'npm run build'
                }
            }
        }

        // ===== FRONTEND DEPLOY ===== //
        stage('Deploy Frontend to Tomcat') {
            steps {
                bat """
                setlocal
                if exist "${TOMCAT_HOME}\\webapps\\reactstudentapi" (
                    rmdir /S /Q "${TOMCAT_HOME}\\webapps\\reactstudentapi"
                )
                mkdir "${TOMCAT_HOME}\\webapps\\reactstudentapi"
                xcopy /E /I /Y ${FRONTEND_DIST}\\* "${TOMCAT_HOME}\\webapps\\reactstudentapi"
                endlocal
                """
            }
        }

        // ===== BACKEND BUILD ===== //
        stage('Build Backend') {
            steps {
                dir('STUDENTAPI-SPRINGBOOT') {
                    bat 'mvn -B clean package' // '-B' for batch mode (CI best practice)
                }
            }
        }

        // ===== BACKEND DEPLOY ===== //
        stage('Deploy Backend to Tomcat') {
            steps {
                bat """
                setlocal
                if exist "${TOMCAT_HOME}\\webapps\\springbootstudentapi.war" (
                    del /Q "${TOMCAT_HOME}\\webapps\\springbootstudentapi.war"
                )
                if exist "${TOMCAT_HOME}\\webapps\\springbootstudentapi" (
                    rmdir /S /Q "${TOMCAT_HOME}\\webapps\\springbootstudentapi"
                )
                for %%F in (${BACKEND_WAR}\\*.war) do (
                    copy "%%F" "${TOMCAT_HOME}\\webapps\\springbootstudentapi.war"
                )
                endlocal
                """
            }
        }
    }
    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed.'
        }
    }
}
