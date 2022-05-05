```dashboard:open-url
url: https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/1.4/vmware-tanzu-kubernetes-grid-14/GUID-index.html
```

<p style="color:blue"><strong> Click here to test the execution in terminal</strong></p>

```execute-1
echo "Hello, Welcome to Partner workshop session"
```

###### Click here to check the Tanzu version

```execute
tanzu version
```

```execute
az --version
```

```execute
kubectl version
```

```execute-all
export SESSION_NAME={{ session_namespace }}
```

###### SE will provide the AZ Credentials, edit and execute in terminal

```copy-and-edit
az login --service-principal -u <App ID> -p <Password> --tenant <Tenent ID> 
```
###### SE will provide the Subscription ID, edit and execute in terminal

```copy-and-edit
az account set --subscription <subscriptionid>
```

```execute
az aks get-credentials --resource-group tap-partner-demo --name {{ session_namespace }}-cluster
```

```execute
kubectl config get-contexts
```

```execute
kubectl create ns tap-install
```

```copy-and-edit
kubectl create secret docker-registry registry-credentials --docker-server=tappartnerdemoacr.azurecr.io --docker-username=tappartnerdemoacr --docker-password=<password> -n tap-install
```
```execute
export INSTALL_BUNDLE=registry.tanzu.vmware.com/tanzu-cluster-essentials/cluster-essentials-bundle@sha256:ab0a3539da241a6ea59c75c0743e9058511d7c56312ea3906178ec0f3491f51d
export INSTALL_REGISTRY_HOSTNAME=registry.tanzu.vmware.com
```

```copy-and-edit
export INSTALL_REGISTRY_USERNAME=<Tanzu Network Registry username>
```

```copy-and-edit
export INSTALL_REGISTRY_PASSWORD=<Tanzu Network password>
```

```copy-and-edit
export DOCKER_REGISTRY_PASSWORD=<ACR Repo password>
```

```execute
cd $HOME/tanzu-cluster-essentials
```

```execute
./install.sh -y
```

```execute
docker login tappartnerdemoacr.azurecr.io -u tappartnerdemoacr -p $DOCKER_REGISTRY_PASSWORD
```

```execute
docker login registry.tanzu.vmware.com -u $INSTALL_REGISTRY_USERNAME -p $INSTALL_REGISTRY_PASSWORD
```

```execute
sudo tanzu secret registry add tap-registry --username tappartnerdemoacr --password $DOCKER_REGISTRY_PASSWORD --server tappartnerdemoacr.azurecr.io --export-to-all-namespaces --yes --namespace tap-install
```

```execute
sudo tanzu package repository add tanzu-tap-repository --url tappartnerdemoacr.azurecr.io/tap-demo/tap-packages:1.1.0 --namespace tap-install
```

```execute
sudo tanzu package repository get tanzu-tap-repository --namespace tap-install
```

```execute
sudo tanzu package available list --namespace tap-install
```

```execute-1
echo $DOCKER_REGISTRY_PASSWORD
```

```editor:open-file
file: ~/tap-values.yaml
line: 6
```

```editor:open-file
file: ~/tap-values.yaml
line: 7
```

```editor:open-file
file: ~/tap-values.yaml
line: 8
```

```editor:open-file
file: ~/tap-values.yaml
line: 40
```

```execute
sudo tanzu package install tap -p tap.tanzu.vmware.com -v 1.1.0 --values-file $HOME/tap-values.yaml -n tap-install
```

```execute
tanzu package installed list -A
```

image: expected

```execute
tanzu package installed get tap -n tap-install
```

```execute
tanzu apps cluster-supply-chain list
```

Tap-gui

```execute
kubectl apply -f $HOME/developer.yaml -n tap-install
```

```execute
kubectl apply -f $HOME/tekton-pipeline.yaml -n tap-install
```

```execute
kubectl apply -f $HOME/scanpolicy.yaml -n tap-install
```

```execute
sudo tanzu package install grype-scanner --package-name grype.scanning.apps.tanzu.vmware.com --version 1.0.0  --namespace tap-install -f $HOME/ootb-supply-chain-basic-values.yaml
```

```execute
sudo tanzu apps workload create tanzu-java-web-app  --git-repo https://github.com/Eknathreddy09/tanzu-java-web-app --git-branch main --type web --label apps.tanzu.vmware.com/has-tests=true --label app.kubernetes.io/part-of=tanzu-java-web-app -n tap-install --yes
```

```execute
sudo tanzu apps workload get tanzu-java-web-app -n tap-install
```

```execute
sudo tanzu apps workload tail tanzu-java-web-app --since 10m --timestamp -n tap-install
```