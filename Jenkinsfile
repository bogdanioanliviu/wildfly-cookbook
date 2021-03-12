 pipeline {
     agent none

     environment {
         registryCredential = 'docker_hub'
         dockerImage = ''
         jmeter_result = '/home/bogdan/PycharmProjects/jmeter_tests/app-bar_result.jtl'
         jmeter_test = '/home/bogdan/PycharmProjects/jmeter_tests/jenkins_app-bar.jmx'
         jmeter_report = '/home/bogdan/PycharmProjects/jmeter_tests/app-bar_reports'
     }

     stages {
         stage('Build') {
             agent {
                 docker {
                     image 'maven:3-alpine'
                     args '-v /home/bil/.m2:/root/.m2 -u 1000'
                 }
             }
             steps {
                 // Get some code from a GitHub repository
                 git 'https://github.com/bogdanioanliviu/wildfly-cookbook.git'
                 //git branch: "${params.branch}", url: 'https://github.com/bogdanioanliviu/wildfly-cookbook.git'
                 sh "mvn -f app-bar-with-this-context/pom.xml clean package org.cyclonedx:cyclonedx-maven-plugin:makeBom org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom"
                 lastChanges (format: 'LINE', matchWordsThreshold: '0.25', matching: 'NONE',
                     matchingMaxComparisons: '1000', showFiles: true, since: 'PREVIOUS_REVISION',
                     specificBuild: '', specificRevision: '', synchronisedScroll: true, vcsDir: ''
                 )
             }

             post {
                 // If Maven was able to run the tests, even if some of the test
                 // failed, record the test results and archive the jar file.
                 success {
                     archiveArtifacts 'app-bar-with-this-context/target/*.war'
                 }
                 failure {
                     echo "Code didn't compile. Fix errores."
                 }
             }
         }
     }
 }