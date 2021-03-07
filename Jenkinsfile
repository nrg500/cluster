node {
    deleteDir()
    stage("Deploy") {
        sh "kubectl apply -k ."
    }
    stage("Cleanup namespaces") {
        def namespacesInCluster = sh(returnStdout: true, script: 'kubectl get ns | sed "s/|/ /" | awk "{print $1}"')
        def namespacesInRepo = sh(returnStdout: true, script: 'dir namespaces')
        print namespacesInCluster
        print namespacesInRepo
    }
}