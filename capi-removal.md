
# Remove CAPI + CAPZ (or other providers)

## via Clusterctl CLI
https://cluster-api.sigs.k8s.io/clusterctl/commands/delete.html

##### The following command deletes all of the provider including CRDs and namespaces except the provider resource

    clusterctl delete --all --include-crd --include-namespaces

##### Remove cert manager resources if installed by clusterctl

    kubectl delete ns cert-manager

##### This command deletes all CAPI CRDs including the Cert-manager CRDs that were installed via CLI

    kubectl delete crds -l clusterctl.cluster.x-k8s.io=""

## via Kubectl

    kubectl delete ns -l clusterctl.cluster.x-k8s.io=""
    kubectl delete crd -l clusterctl.cluster.x-k8s.io=""
    kubectl delete crd providers.clusterctl.cluster.x-k8s.io
