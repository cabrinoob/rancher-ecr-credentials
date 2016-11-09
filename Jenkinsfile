properties([disableConcurrentBuilds()])
node ('Archis - awsbld001') {
    step ([$class: 'CopyArtifact', projectName: 'fmeriot/rancher-ecr-credentials']);
    wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"])
        {
        def URL = 'http://git.intraxiti.com/golangtag/gollector.git'
        def REGISTRY = '413177864845.dkr.ecr.eu-west-1.amazonaws.com'
        def servicename = 'ecrcredentials-fork'
        def dockertag = env.BRANCH_NAME
        if(dockertag=="master")
        dockertag="latest"
        git branch: "${env.BRANCH_NAME}", credentialsId: '7bc32aa3-2c7d-44d1-8689-a1f2131e22ff', poll:true,url: "${URL}"
        stage name: 'Docker image construction', concurrency: 1
            withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'awsdev-archis-microservices-dockerpublisher', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                sh("docker login -u AWS -p \$(aws ecr get-login  --region eu-west-1 | cut -d ' ' -f 6) ${REGISTRY}")
                docker.build("${REGISTRY}/${servicename}:${dockertag}")//.push("${env.BRANCH_NAME}")
                sh("docker push ${REGISTRY}/${servicename}:${dockertag}")
            }
        stage name: 'notifications'
            slackSend channel: "#microservices",  message: "Build Done : ${env.JOB_NAME}\nBranch : ${env.BRANCH_NAME}\nDuration : " + manager.build.durationString + " (<${env.BUILD_URL}|Open>)"
            /*if(dockertag=="develop"){
                build job: 'Archis/microservices-deploy', parameters: [string(name: 'stack', value: "${servicename}-${env.BRANCH_NAME}"), string(name: 'service', value: "${servicename}"), string(name: 'rancherserver', value: 'https://rancher-server.atclouddev.net/v1'), string(name: 'rancherapikey', value: 'NjBDODEyM0Y3Mjc2MDIzNzgwOTE6ZllNaDZmTUwxRGM0NWJ2WUEzSmFwZlI1QjM0aHdBWEY4eXN3dXJ3TA==')]
            }*/
    }
}