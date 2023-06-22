
# Flux

There are a number of ways to install and uninstall Flux. To install Flux for the first time the [flux bootstrap](https://fluxcd.io/flux/cmd/flux_bootstrap/) utility is recommended. To uninstall Flux the [flux uninstall](https://fluxcd.io/flux/cmd/flux_uninstall/) utility is recommended. If you use Flux bootstrap to deploy Flux you can upgrade using [flux bootstrap](https://fluxcd.io/flux/installation/#bootstrap-upgrade).

Whatever mechanism you use, if you subsequently need to uninstall and reinstall you can follow the following process.

## Uninstall Flux

If you uninstall Flux using the flux uninstall command it will remove all flux custom resources too. In the case of HelmReleases and also for  Kustomizations that have prune set to true this will result in the removal of all the resources that were deployed by Flux. If you want to keep these resources you should first suspend the HelmReleases and Kustomizations.

```bash
source <(kubectl get kustomizations -A \
  -o=jsonpath='{range .items[*]}{"flux suspend kustomization -n "}\
  {.metadata.namespace}{" "}{.metadata.name}{"\n"}{end}')
source <(kubectl get HelmReleases -A \
  -o=jsonpath='{range .items[*]}{"echo flux suspend helmrelease -n "}\
  {.metadata.namespace}{" "}{.metadata.name}{"\n"}{end}')
```

Capture the flux-system GitRepository url and flux-system Kustomization path for use during reinstallation.

```bash
flux_path="$(kubectl get kustomization -n flux-system flux-system -o=jsonpath='{@.spec.path}')"
echo "flux_path=$flux_path"
git_url="$(kubectl get gitrepository -n flux-system flux-system -o=jsonpath='{@.spec.url}')"
echo "git_url=$git_url"
```

Now you can uninstall Flux.

```bash
flux uninstall --keep-namespace --silent
```

Retaining the flux-system namespace is optional. Retaining it will prevent the deletion of secrets used by GitRepository and HelmRepository resources in that namespace.

## Reinstall Flux

To resinstall flux, first install the flux CLI. The following example installs version 2.0.0-rc.5. If you omit the version it will install the latest version.

```bash
export FLUX_VERSION=2.0.0-rc.5
curl -s https://fluxcd.io/install.sh | bash
flux version
```

This will reinstall the Flux custom resource definitions and controllers but not the custom resource.

To reinstall the custom resources.
  
```bash
git clone $url
repo_name="$(echo $url | awk -F/ '{print $NF}')"
kubectl apply -f /$path/flux-system/gotk-sync.yaml
```

This will reinstall the flux-system GitRepository and Kustomization which will in turn reinstall the other custom resources. If you have any Flux custom resources that are not installed directly or indirectly by the flux-system Kustomization you will need to reinstall them manually.
