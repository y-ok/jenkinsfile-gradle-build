pipeline {
   agent any
   environment {
       javadocDir = 'build/docs/javadoc'
       testReportDir = 'build/test-results/test'
       jacocoReportDir = 'build/jacoco'
   }
   stages {
      stage('事前準備') {
         steps {
             deleteDir()
             git url: 'https://github.com/y-ok/gradle-java-project.git', branch: 'master'
             gradlew 'clean'
         }
      }
      
      stage('コンパイル') {
          steps {
              gradlew 'classes testClasses'
          }
      }
      
      stage('Javadoc') {
          steps {
              gradlew 'javadoc -x classes'
              publishHTML([
                  allowMissing: false,
                  alwaysLinkToLastBuild: false,
                  keepAll: true,
                  reportDir: "${javadocDir}",
                  reportFiles: 'index.html', 
                  reportName: 'Javadocレポート'
                  ])
          }
      }
      
      stage('テスト') {
          steps {
              gradlew 'test jacocoTestReport -x classes -x testClasses'
              junit allowEmptyResults: true, testResults: "${testReportDir}/*.xml"
              archiveArtifacts allowEmptyArchive: true, artifacts: "${testReportDir}/*.xml"
              
              publishHTML([
                  allowMissing: false,
                  alwaysLinkToLastBuild: false,
                  keepAll: true,
                  reportDir: 'build/reports/tests/test', 
                  reportFiles: 'index.html', 
                  reportName: 'JUnit実行レポート'
                  ])
          }
      }
   }
}

// Gradlewコマンドを実行する
def gradlew(command) {
    if(isUnix()) {
        sh "./gradlew ${command} --stacktrace"
    } else {
        bat "./gradlew.bat ${command} --stacktrace"
    }
}
