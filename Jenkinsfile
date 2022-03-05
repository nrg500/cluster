node {
    deleteDir()
    stage("Checkout") {
        checkout scm
    }
      stage("Cleanup namespaces") {
        def sysNamespaces = ["kube-system", "kube-public", "kube-node-lease", "default", "metallb-system"];
        def namespacesInCluster = sh(returnStdout: true, script: '''echo $(kubectl get --no-headers ns | sed 's/|/ /' | awk '{print $1}')''').split("\\s+");
        def namespacesInRepo = sh(returnStdout: true, script: 'dir namespaces').split("\\s+");
        for (namespaceInCluster in namespacesInCluster) {
            if(namespacesInRepo.contains(namespaceInCluster)) {
                print "${namespaceInCluster} is present in repo do nothing."
            } else if(!sysNamespaces.contains(namespaceInCluster)) {
                print "deleting: ${namespaceInCluster}"
                sh "kubectl delete namespace ${namespaceInCluster}"
            }
        }
    }
    stage("Replace secrets") {
        withCredentials([
        string(
            credentialsId: 'google-analytics-id',
            variable: 'ga_id')
        ]) {
            print "${ga_id}"
        sh """sed -i "s|google-analytics-id: \"\"|google-analytics-id: \"${ga_id}\"|g" services/blog/secret.yaml"""
        }
    }
    stage("Deploy") {
        sh "kubectl apply -k ."
    }
}