
# Remove CAPI + CAPZ

## via Clusterctl CLI
https://cluster-api.sigs.k8s.io/clusterctl/commands/delete.html

##### The following command deletes all of the provider including CRDs and namespaces except the provider resource

    clusterctl delete --all --include-crd --include-namespaces

#### *WARNING* : The following steps are to remove cert manager resources

#####  If cert-manager was not used prior to clusterctl. You can remove it in its entirety by deleting the namespace and then continuing on
    kubectl delete ns cert-manager

#####  If cert-manager was used prior to clusterctl. Use the following   

    TODO


##### This command deletes all remaining CAPI CRDs including the Cert-manager CRDs that were installed via CLI

    kubectl delete crds -l clusterctl.cluster.x-k8s.io=""

## via Kubectl

##### The following commands will delete the namespaces, CRDs that are related to clusterctl (CAPI). The resources should have been labeled with the following label 
> *clusterctl.cluster.x-k8s.io=""*

    kubectl delete ns -l clusterctl.cluster.x-k8s.io=""
    kubectl delete crd -l clusterctl.cluster.x-k8s.io=""
    kubectl delete crd providers.clusterctl.cluster.x-k8s.io

