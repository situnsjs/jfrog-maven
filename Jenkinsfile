node {
    def server = Artifactory.server('situnsjs.jfrog.io')
    def buildInfo = Artifactory.newBuildInfo()
    def rtMaven = Artifactory.newMavenBuild()
    
    
    stage ('Checkout & Build') {
        git url: 'https://github.com/situnsjs/jfrog-maven.git'
    }
 
    stage ('Unit Test') {
        rtMaven.tool = 'Maven-3.6.0' // Tool name from Jenkins configuration
        rtMaven.run pom: 'pom.xml', goals: 'clean compile test'
    }
    
    stage('SonarQube Analysis') {
       // rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean org.jacoco:jacoco-maven-plugin:prepare-agent package sonar:sonar -Dsonar.host.url=https://sonarcloud.io -Dsonar.organization=situnsjs -Dsonar.login=f34e20399daf8cb714ba0a54f8f368d6fcfc211c '
         
        rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean install sonar:sonar -Dsonar.host.url=https://sonarcloud.io -Dsonar.organization=situnsjs -Dsonar.login=f34e20399daf8cb714ba0a54f8f368d6fcfc211c '
      }
    
    stage ('Artifactory configuration') {
        // Obtain an Artifactory server instance, defined in Jenkins --> Manage..:
         
        rtMaven.tool = 'Maven-3.6.0' // Tool name from Jenkins configuration
        rtMaven.deployer releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot', server: server
        rtMaven.deployer.deployArtifacts = false // Disable artifacts deployment during Maven run
     }
            
    stage ('Install') {
        rtMaven.run pom: 'pom.xml', goals: 'install', buildInfo: buildInfo
     }
 
    stage ('Deploy') {
        rtMaven.deployer.deployArtifacts buildInfo
    }
        
    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
    
       
}
