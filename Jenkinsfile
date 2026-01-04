pipeline {
    agent any

    environment {
        // Paths
        DOTNET_PATH   = "C:\\Program Files\\dotnet\\dotnet.exe"
        PUBLISH_DIR   = "C:\\Jenkins\\publish"
        IIS_SITE_PATH = "C:\\inetpub\\wwwroot\\MyDotNetApp"

        // IIS Site / App Pool Name
        IIS_SITE_NAME = "MyDotNetApp"
        IIS_APPPOOL   = "MyDotNetApp"

        // Project file
        PROJECT_PATH  = "webapp.csproj"
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

        stage('Stop App Pool') {
            steps {
                bat "%windir%\\system32\\inetsrv\\appcmd stop apppool /apppool.name:\"%IIS_APPPOOL%\""
            }
        }

        stage('Deploy to IIS') {
            steps {
                bat """
                xcopy "%PUBLISH_DIR%\\*" "%IIS_SITE_PATH%\\" /s /e /y /i
                """
            }
        }

        stage('Start App Pool') {
            steps {
                bat "%windir%\\system32\\inetsrv\\appcmd start apppool /apppool.name:\"%IIS_APPPOOL%\""
            }
        }
    }

    post {
        success {
            echo "✅ Deployment to IIS completed successfully."
        }
        failure {
            echo "❌ Deployment failed."
        }
    }
}
