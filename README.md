#### Testing out some kustomize functionality.

The bases for all of these applications are simply the manifest files from each respective repository. In each base directory, there is a kustomization file which points to the resource manifest we're deploying. It gets a bit more interesting when you consider multiple clusters you'd be deploying to, for example in the overlays in the kubeless directory. 

Consider we've got a client A that we're deploying kubeless into their cluster. However, they don't want kubeless in the default `kubeless` namespace, and istead want it in `clienta-kubeless`. We can add a kustomization file to specify that we wish to deploy a new resource, the new namespace manifest. We also specify that we wish for all resources to be deployed in `clienta-kubeless` by specifying the `namespace` in the kustomization file. This will overwrite all namespaces mentioned in the base. A quick diff between the generated manifests can show this off:
```bash
$ diff base.yaml clienta.yaml
0a1,5
> apiVersion: v1
> kind: Namespace
> metadata:
>   name: clienta-kubeless
> ---
44c49
<   namespace: kubeless
---
>   namespace: clienta-kubeless
163c168
<   namespace: kubeless
---
>   namespace: clienta-kubeless
340c345
<   namespace: kubeless
---
>   namespace: clienta-kubeless
348c353
<   namespace: kubeless
---
>   namespace: clienta-kubeless
```


If we want to install the kubeless app for client A, then it's just doing a `kustomize build kubeless/overlays/client-a` from the root of this repo to verify the yaml that's getting output, followed by a `kustomize build kubeless/overlays/client-a | kubectl apply -f -` to install it to the cluster.