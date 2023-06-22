
# Remove CAPI + CAPZ

## via Clusterctl CLI
https://cluster-api.sigs.k8s.io/clusterctl/commands/delete.html

#### *WARNING* : Delete the Cluster Objects from Flux Repository before beginning the removal of CAPI Controller or lingering resources in your Cloud Provider (Azure, AWS, etc) 

##### The following command deletes all of the provider including CRDs and namespaces except the provider resource though sometimes there are lingering resources due to timing, or admissioncontrol. Use the following Kubectl steps to further clean your cluster.

    clusterctl delete --all --include-crd --include-namespace

#### *WARNING* : The following steps are to remove cert manager resources

#####  If cert-manager was NOT used prior to clusterctl. You can remove it in its entirety by deleting the namespace and then continuing on
    kubectl delete ns cert-manager

#####  If cert-manager was used prior to clusterctl. Use the following to delete only clusterctl related cert-manager resources

    kubectl delete --all-namespaces certficates -l clusterctl.cluster.x-k8s.io=""

##### This command deletes all remaining CAPI CRDs including the Cert-manager CRDs that were installed via CLI

    kubectl delete crds -l clusterctl.cluster.x-k8s.io=""

##### This command deletes the secrets related to the ClusterAPI Controllers and Providers

    kubectl delete --all-namespaces secret --field-selector type=cluster.x-k8s.io/secret

##### The following command deletes the Mutatingwebhookconfigurations and Validatingwebhooksconfigurations that were created
    
    kubectl delete mutatingwebhookconfigurations -l clusterctl.cluster.x-k8s.io=""
    kubectl delete validatingwebhookconfigurations -l clusterctl.cluster.x-k8s.io=""
    
## via Kubectl

##### The following commands will delete the namespaces, CRDs that are related to clusterctl (CAPI). The resources should have been labeled with the following label 
> *clusterctl.cluster.x-k8s.io=""*

    kubectl delete ns -l clusterctl.cluster.x-k8s.io=""
    kubectl delete crd -l clusterctl.cluster.x-k8s.io=""
    kubectl delete crd providers.clusterctl.x-k8s.io=""
    kubectl delete --all-namespaces secret --field-selector type=cluster.x-k8s.io/secret


##### The following command deletes the Mutatingwebhookconfigurations and Validatingwebhooksconfigurations that were created
    
    kubectl delete mutatingwebhookconfigurations -l clusterctl.cluster.x-k8s.io=""
    kubectl delete validatingwebhookconfigurations -l clusterctl.cluster.x-k8s.io=""





    
