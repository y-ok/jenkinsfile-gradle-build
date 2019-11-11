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
      
      stage('JavaDoc') {
          steps {
              gradlew 'javadoc -x classes'
              step([
                  $class: 'JavadocArchiver',
                  javadocDir: "${javadocDir}",
                  keepAll: true
                  ])
          }
      }
      
      stage('テスト') {
          steps {
              gradlew 'test jacocoTestReport -x classes -x testClasses'
              junit allowEmptyResults: true, testResults: "${testReportDir}/*.xml"
              archiveArtifacts "${testReportDir}/*.xml", allowEmptyArchive: true
              
              step([
                  $class: 'JacocoPublisher',
                  execPattern: "${jacocoReportDir}/*.exec",
                  exclusionPattern: '**/*Test.class'
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
