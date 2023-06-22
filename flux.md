
# Flux

There are a number of ways to install and uninstall Flux. To install Flux for the first time the [flux bootstrap](https://fluxcd.io/flux/cmd/flux_bootstrap/) utility is recommended. To uninstall Flux the [flux uninstall](https://fluxcd.io/flux/cmd/flux_uninstall/) utility is recommended. If you use Flux bootstrap to deploy Flux you can upgrade using [flux bootstrap](https://fluxcd.io/flux/installation/#bootstrap-upgrade).

Whatever mechanism you use, if you subsequently need to uninstall and reinstall you can follow the following process.

## Uninstall Flux

Capture the flux-system GitRepository url and flux-system Kustomization path for use during reinstallation.

```bash
flux_path="$(kubectl get kustomization -n flux-system flux-system -o=jsonpath='{@.spec.path}')"
echo "flux_path=$flux_path"
git_url="$(kubectl get gitrepository -n flux-system flux-system -o=jsonpath='{@.spec.url}')"
echo "git_url=$git_url"
repo_name="$(echo $git_url | awk -F/ '{print $NF}')"
```

Now you can uninstall Flux.

```bash
flux uninstall --keep-namespace --silent
```

Retaining the flux-system namespace is optional. Retaining it will prevent the deletion of secrets used by GitRepository and HelmRepository resources in that namespace. Flux uninstall will remove the Flux finalizer from the custom resources so objects deployed by Flux will not be deleted.

## Reinstall Flux

To resinstall flux, first install the flux CLI. The following example installs version 2.0.0-rc.5. If you omit the version it will install the latest version.

```bash
export FLUX_VERSION=2.0.0-rc.5
curl -s https://fluxcd.io/install.sh | bash
flux install --export > gotk-components.yaml
```

And replace the file in the git repository with the new version, i.e.

```bash
git clone $git_url
cp gotk-components.yaml $repo_name/$flux_path/flux-system/gotk-components.yaml
pushd $repo_name
git add $flux_path/flux-system/gotk-components.yaml
git commit -m "Update gotk-components.yaml"
git push
```

To reinstall Flux custom resource definitions and controllers and custom resources.
  
```bash
flux bootstrap git --url $git_url --path $flux_path
```

This will reinstall the flux-system GitRepository and Kustomization which will in turn reinstall the other custom resources. If you have any Flux custom resources that are not installed directly or indirectly by the flux-system Kustomization you will need to reinstall them manually.
