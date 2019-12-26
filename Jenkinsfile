pipeline {
  agent {
    docker {
      image 'maven:3-jdk-8-slim'
      label 'linux-agents'
      reuseNode true
    }
  }
  options {
    timeout(time: 10, unit: 'MINUTES')
  }
  stages {
    stage('Package') {
      steps {
        // at the moment it's impossible to run tests on Jenkins, because of unnamed container user (-u 1000:1000)
        // java.io.IOException: failure to login
        // Cause: javax.security.auth.login.LoginException: java.lang.NullPointerException: invalid null input: name
        sh 'mvn package -DskipTests'
      }
    }
    stage('Deploy') {
      when {
        // publish only for `master`
        branch 'master'
      }
      steps {
        sh """mvn deploy \
          --settings ${WORKSPACE}/settings.xml \
          -DskipTests \
          -Dmaven.install.skip=true \
          -Drepoid=delphi-nexus \
          -Dreponame=Nexus \
          -Drepourl=https://artifacts.apollo.stream/repository/maven-releases/
          """
      }
    }
  }
}
