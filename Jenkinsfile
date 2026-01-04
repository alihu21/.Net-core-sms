pipeline {
    agent any

    environment {
        // Paths
        DOTNET_PATH = "C:\\Program Files\\dotnet\\dotnet.exe"
        PUBLISH_DIR = "C:\\Jenkins\\publish"
        IIS_SITE_PATH = "C:\\inetpub\\wwwroot\\MyDotNetApp"

        // IIS Site Name
        IIS_SITE_NAME = "MyDotNetApp"

        // Project file
        PROJECT_PATH = "webapp.csproj"
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Restore Packages') {
            steps {
                bat "\"%DOTNET_PATH%\" restore \"%PROJECT_PATH%\""
            }
        }

        stage('Build') {
            steps {
                bat "\"%DOTNET_PATH%\" build \"%PROJECT_PATH%\" --configuration Release --no-restore"
            }
        }

        stage('Publish') {
            steps {
                bat """
                if exist "%PUBLISH_DIR%" rmdir /s /q "%PUBLISH_DIR%"
                "%DOTNET_PATH%" publish "%PROJECT_PATH%" --configuration Release --output "%PUBLISH_DIR%"
                """
            }
        }

        stage('Stop IIS Site') {
            steps {
                bat """
                %windir%\\system32\\inetsrv\\appcmd stop site "%IIS_SITE_NAME%"
                """
            }
        }

        stage('Deploy to IIS') {
            steps {
                bat """
                xcopy "%PUBLISH_DIR%\\*" "%IIS_SITE_PATH%\\" /s /e /y /i
                """
            }
        }

        stage('Start IIS Site') {
            steps {
                bat """
                %windir%\\system32\\inetsrv\\appcmd start site "%IIS_SITE_NAME%"
                """
            }
        }
    }

    post {
        success {
            echo "Deployment to IIS completed successfully."
        }
        failure {
            echo "Deployment failed."
        }
    }
}


