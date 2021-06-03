try{
    node{
    stage('code checkout from github'){
        echo "Fetching code from git repo to jenkins directory"
        git 'https://github.com/mitranmukesh/batch10-1.git'
    }
    stage('Maven build build,test and package'){
        tool name: 'maven', type: 'maven'
        echo "Building package"
        sh 'mvn clean test package'
        //sh 'mvn test site'
    }
    
    stage('sonar scan'){
        echo "scanning applications"
        tool name: 'maven', type: 'maven'
        //sh 'mvn sonar:sonar -Dsonar.projectKey=sonar -Dsonar.login=admin -Dsonar.password=admin123 -Dsonar.sources=/var/lib/jenkins/workspace/CI_CD-case-study -Dsonar.host.url=http://34.126.189.169:9000'
        sh 'mvn sonar:sonar -Dsonar.projectKey=sonar -Dsonar.login=admin -Dsonar.password=admin123 -Dsonar.host.url=http://34.126.176.109:9000'
    }
    stage('HTML report'){
        echo "HTML report"
        //([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/case-study_CI-CD', reportFiles: 'pom.xml', reportName: 'HTML Report', reportTitles: 'CI-CD report'])
        //publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/CI_CD-case-study/target', reportFiles: '', reportName: 'HTML', reportTitles: 'CI-CD report'])
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/CI_CD-case-study/target/surefire-reports', reportFiles: '', reportName: 'HTML Report', reportTitles: 'HTML'])
        echo "fecthing this index.html from source to show an html report"
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'src/main/resources/static/', reportFiles: 'index.html', reportName: 'HTML', reportTitles: 'CI-CD report'])
    }
    stage("Docker image"){
        echo "Docker image"
        def docker = tool name: 'docker', type: 'dockerTool'
        def dockerCMD = "${docker}/bin/docker"
        sh "${dockerCMD} --version"
        sh "${dockerCMD} build -t mitranmukesh/my-test-apps:1.6 ."
        echo "binding credentials to push image into docker hub"
        withCredentials([usernamePassword(credentialsId: 'docker cred', passwordVariable: 'userPass', usernameVariable: 'userName')]) {
                sh "${dockerCMD} login -u ${userName} -p ${userPass}"
                sh "${dockerCMD} push mitranmukesh/my-test-apps:1.6"
            }
    }
    stage ("Ansible playbook"){
        echo "Ansible playbook"
        ansiblePlaybook credentialsId: 'AWS_master', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'deploy-playbook.yml'
    }
    }
}
catch(Exception error){}
finally{
    stage ("email"){
        echo "email not"
        emailext attachLog: true, body: '''$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:
        Check console output at $BUILD_URL to view the results.''', compressLog: true, subject: 'Build status', to: 'mitranmukesh@gmail.com'
    
        //emailext attachLog: true, body: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:', subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:', to: 'mukeshmitran0@gmail.com'
    
        //emailext body: 'build passed', subject: 'project status', to: 'mukeshmitran0@gmail.com'
    }
}