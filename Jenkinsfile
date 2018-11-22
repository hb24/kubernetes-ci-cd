node {

    checkout scm

    env.DOCKER_API_VERSION="1.23"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "127.0.0.1:30400/"
    imageName = "${registryHost}${appName}"
    taggedImageName = "${imageName}:${tag}"
    env.BUILDIMG=imageName

    stage "Build"
        /*
         * We need to add the image tag separate to ensure the latest tag gets auto updated in the registry.
         * https://medium.com/@mccode/the-misunderstood-docker-tag-latest-af3babfd6375
         */
        sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
        sh "docker tag ${imageName} $taggedImageName}"

    stage "Push"

        sh "docker push ${imageName}"

    stage "Deploy"

        kubernetesDeploy configs: "applications/${appName}/k8s/*.yaml", kubeconfigId: 'kenzan_kubeconfig'

}