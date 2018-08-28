
node('modjk') {
// Delete the workspace
//deleteDir()
stage('Retrieve source code') {
    checkout scm
    delivery = load 'repository.groovy'
    sh " cd $WORKSPACE;/bin/mkdir Build-${env.BUILD_NUMBER} "
    }
try {
    stage('Maven Build') {
      docker.image('maven:3.5-jdk-8-alpine').inside {
        sh "mvn clean package -Dbuild.number=${BUILD_NUMBER}"
        sh "/bin/mv -f $WORKSPACE/target/*.war $WORKSPACE/Build-${env.BUILD_NUMBER}/samba_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war"
       }
    }
   stage('Deploy') {
        sh "/bin/cp -f $WORKSPACE/Build-${env.BUILD_NUMBER}/samba_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war /opt/apache-tomcat-8.5.33/webapps/samba.war"
        sh"/bin/rm -rf /opt/apache-tomcat-8.5.33/webapps/ROOT/*"
        sh"/bin/mv -f /opt/apache-tomcat-8.5.33/webapps/samba/* /opt/apache-tomcat-8.5.33/webapps/ROOT/*"
   }
  
   delivery.artifactory()
  }
  catch (e) {
      currentBuild.result = "FAILED"
      throw e
    }
}

