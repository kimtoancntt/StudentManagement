pipeline {
    agent any

    environment {
        GIT_BRANCH = 'DEV'
        GIT_USERNAME = 'kimtoancntt'
        GIT_TOKEN = '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'
        GIT_REPO_URL = "https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/${GIT_USERNAME}/StudentManagement.git"
        ENV = 'Development'
        BUILD_CONFIG = 'Release'
        DOTNET_VERSION = 'net8.0'
        SLN = '.\\StudentManagement\\src\\StudentManagement.Api\\StudentManagement.Api.csproj'
        WEB_SITE = 'studentmanagement.dev.com'
        APP_POOL = 'studentmanagement.dev.com'
        PUBLISH_PATH = '.\\StudentManagement\\src\\StudentManagement.Api\\bin\\%BUILD_CONFIG%\\%DOTNET_VERSION%\\publish'
        WWW_ROOT = 'C:\\WWW\\StudentManagement\\BE\\DEV'

        SlnUnitTest = '.\\StudentManagement\\StudentManagement.sln'
        TestResultFileName = 'UnitTestResult'
        TrxFilePath = '.\\StudentManagement\\tests\\StudentManagement.Architecture.Tests\\TestResults'
        MainDirectory = 'C:\\WWW\\StudentManagement\\TestResults\\'
    }

    stages {
        stage('Git Checkout') {
            steps {
                echo "Git Repository URL: ${GIT_REPO_URL}"
                git branch: "${env.GIT_BRANCH}", url: "${env.GIT_REPO_URL}"
            }
        }
        stage('Restore Nuget Package') {
            steps {
                echo "Restore url: ${env.SLN}"
                bat "dotnet restore ${env.SLN}"
            }
        }
        stage('Clean') {
            steps {
                bat "dotnet clean ${env.SLN}"
            }
        }
        stage('Build') {
            steps {
                bat "dotnet build ${env.SLN} --configuration ${env.BUILD_CONFIG}"
            }
        }
        stage('Unit Test') {
            steps {
                bat "dotnet test ${env.SlnUnitTest} -l:trx;LogFileName=${env.TestResultFileName}"
                bat "if not exist ${env.MainDirectory} mkdir ${env.MainDirectory}"
                bat "copy ${env.TrxFilePath}\\${env.TestResultFileName} ${env.MainDirectory}"
            }
        }
        stage('publish') {
            steps {
                bat "dotnet publish ${env.SLN} /p:Configuration=${env.BUILD_CONFIG} /p:EnvironmentName=${env.ENV}"
            }
        }
        stage('Stop IIS SERVER') {
            steps{
                bat "%windir%\\system32\\inetsrv\\appcmd stop sites ${env.WEB_SITE}%"
                bat "%windir%\\system32\\inetsrv\\appcmd stop apppool /apppool.name:${env.APP_POOL}%"
                bat "echo watting untill server stopped"
                bat "ping google.com /n 5"
            }
        }
        stage('Copy to hosted website folder'){
            steps{
                bat "xcopy ${env.PUBLISH_PATH} ${WWW_ROOT} /e /y /i /r"
            }
        }
        stage('Start service') {
            steps {
                bat "%windir%\\system32\\inetsrv\\appcmd start apppool /apppool.name:${env.APP_POOL}"
                bat "%windir%\\system32\\inetsrv\\appcmd start sites ${env.WEB_SITE}%"
            }
        }
    }
}