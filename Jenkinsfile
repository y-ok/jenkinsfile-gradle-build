pipeline {
   agent any
   environment {
       javadocDir = 'build/docs/javadoc'
       testReportDir = 'build/reports/tests/test'
       spotBugsDir = 'build/reports/spotbugs'
       jacocoReportDir = 'build/jacocoHtml'
   }
   stages {
      stage('事前準備') {
         steps {
             deleteDir()
             git url: 'https://github.com/y-ok/gradle-java-project.git', branch: 'master'
             gradlew 'clean'
         }
      }
      
      stage('静的解析') {
          steps {
              gradlew 'spotbugsMain'              
              publishHTML([
                  allowMissing: false,
                  alwaysLinkToLastBuild: false,
                  keepAll: true,
                  reportDir: "${spotBugsDir}",
                  reportFiles: 'main.html', 
                  reportName: 'spotbugs解析結果レポート'
                  ])
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
              gradlew 'test jacocoTestReport -x classes'
              publishHTML([
                  allowMissing: false,
                  alwaysLinkToLastBuild: false,
                  keepAll: true,
                  reportDir: "${testReportDir}",
                  reportFiles: 'index.html', 
                  reportName: 'テストレポート'
                  ])
             
              publishHTML([
                  allowMissing: false,
                  alwaysLinkToLastBuild: false,
                  keepAll: true,
                  reportDir: "${jacocoReportDir}",
                  reportFiles: 'index.html', 
                  reportName: 'Jacocoレポート'
                  ])
          }
      }

      stage('リリース作成') {
          steps {
              gradlew 'releaseZip'
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
