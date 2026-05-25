# Argocd-project
A quick recap on how to install and integrate argocd and github to deploy app on kubernetes. I have used minikube cluster for this project. If you don't have minikube installed you can use the link below to install it.

https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download

## Step1 Create install argocd

- Google argocd docs to get argocd documentation and click on getting started.

```bash
kubectl create namespace argocd
kubectl apply -n argocd --server-side --force-conflicts -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
## Verify the pods and services are created in the namespace argocd

```bash
kubectl get pods -n argocd
kubectl get svc -n argocd
```
## Edit the service argocd-server
The service argocd-server has the service type as ClusterIp, since we want the UI of argocd, we need to edit and change the service type from ClusterIp to NodePort.

```bash
kubectl edit svc argocd-server -n argocd
```
## Expose the service argocd-server

```bash 
minikube service list -n argocd
minikube service argocd-server -n argocd
```

![alt text](image-1.png)

## Copy the http url and paste it to your broowser to access argocd UI

## Login Cred
- Username: admin
- Password: Run the bellow commands to get the password

```bash
kubectl get secret -n argocd # Will show argocd-initial-admin-secret 
kubectl edit secret argocd-initial-admin-secret -n argocd # copy the password to be decoded
echo <<PAWSSORD>> | base64 --decode # Decode the password
```

## Create your first application using the UI

- Click on Create application
- Provide the application name ex: my first-app
- Project name: default
- Sync Policy: automatic
- Source: provide your github repo url where you have your K8s manifest files; ex: https://github.com/iris-lali/Argocd-project
- Path: path to your manifest or the folder of your manifest; ex: daemonset
- Destination: Select your cluster url; https://kubernetes.default.svc
- NameSpace: provide your namespace; ex: dev
- And click on create.

## Et Voila!!!!

### Verify your deployment

```bash
kubectl get daemonset -n dev
kubectl get pods -n dev
```

![alt text](image-1.png)

## Create Application using argocd CLI

- Make sure you install argocd CLI, link below
https://argo-cd.readthedocs.io/en/stable/cli_installation/

1) For Mac, Linux and WSL Homebrew
```bash 
brew install argocd
```
2) For Windows
- Download With PowerShell: Invoke-WebRequest¶
You can view the latest version of Argo CD at the link above or run the following command to grab the version:
```bash
$version = (Invoke-RestMethod https://api.github.com/repos/argoproj/argo-cd/releases/latest).tag_name
```
- Replace $version in the command below with the version of Argo CD you would like to download:

```bash
$url = "https://github.com/argoproj/argo-cd/releases/download/" + $version + "/argocd-windows-amd64.exe"
$output = "argocd.exe"

Invoke-WebRequest -Uri $url -OutFile $output
```
- Also please note you will probably need to move the file into your PATH. Use following command to add Argo CD into environment variables PATH

```bash
[Environment]::SetEnvironmentVariable("Path", "$env:Path;C:\Path\To\ArgoCD-CLI", "User")
```


- Use the link below to get argocd command reference
https://argo-cd.readthedocs.io/en/stable/user-guide/commands/argocd/

- Once on the reference page, scrow down and click on argocd app; it will take you to a next page, click on argocd app create.
- Copy one of the example to create your app through the CLI

```bash
argocd app create daemon --repo https://github.com/iris-lali/Argocd-project.git --path daemonset --dest-namespace dev --dest-server https://kubernetes.default.svc --directory-recurse --sync-policy automatic
```
### Login into argocd using the cli

```bash
argocd login SERVER
```
- provide username and password

### Verify your deployment

```bash
kubectl get daemonset -n dev
kubectl get pods -n dev
```









