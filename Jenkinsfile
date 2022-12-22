node {
  
  stage('Clone') {
      dir('.') {
          git branch: 'main', credentialsId: 'github_com', url: 'git@github.com:jvalentino/example-java-maven-jenkins-scripted.git'
      }    
  }       

  stage('Deploy') {
     withCredentials([usernamePassword(
        credentialsId: 'github-publish-maven', 
        passwordVariable: 'MVN_PASSWORD', 
        usernameVariable: 'MVN_USERNAME')]) {

        withMaven(mavenSettingsFilePath: 'settings.xml') {
          sh """
            ./mvnw deploy \
                -Drepo.id=github \
                -Drepo.login=${MVN_USERNAME} \
                -Drepo.pwd=${MVN_PASSWORD} \
                -Drevision=1.${BUILD_NUMBER}
          """
        }  
     }
  }  

  stage('Post') {
    jacoco()
    junit 'target/surefire-reports/*.xml'
    def pmd = scanForIssues tool: [$class: 'Pmd'], pattern: 'target/pmd.xml'
    publishIssues issues: [pmd]
  }
  
}