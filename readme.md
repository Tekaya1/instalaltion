# Kubernetes Setup on WSL2 — Quick Guide

## 1. Install Docker

Docker is required as the Kubernetes runtime.

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

**Note**: Log out and back in or restart WSL for group changes to take effect.

## 2. Install kubectl

kubectl is the Kubernetes command-line tool.

```bash
curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```
if previous command show error the run this :

```bash
sudo apt update
sudo apt install -y curl ca-certificates
```
and reinstall the previous command ==> 2. Install kubectl
## 3. Install minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
```

## 4. Start minikube

```bash
minikube start --driver=docker
```

## 5. Test

```bash
kubectl get nodes
```

# Additionnels config

## 1. Enable kubectl Tab Completion

Enable autocompletion for kubectl commands.

```bash
sudo apt install -y bash-completion
echo 'source <(kubectl completion bash)' >> ~/.bashrc
source ~/.bashrc
```

Test by typing `kubectl get pod<TAB>` for autocomplete.

## 2. Create Alias `k` for kubectl with Tab Completion

Add an alias for kubectl to simplify commands.

```bash
echo 'alias k=kubectl' >> ~/.bashrc
echo 'complete -F __start_kubectl k' >> ~/.bashrc
source ~/.bashrc
```

Now use `k` instead of `kubectl` with full autocomplete support.

## 5. Using Annotations for Rollout Change Causes

configure annotation

```bash
nano ~/.bashrc
```

and add this line at the end of file:

```bash
kchange() {
  if [ -z "$1" ] || [ -z "$2" ]; then
    echo "Usage: kchange <deployment> <message>"
    return 1
  fi
  kubectl annotate deployment "$1" kubernetes.io/change-cause="$2" --overwr>}
```

Then:

```bash
source ~/.bashrc
```

Test annotation:

```bash
  k create deployment web --image=nginx:1.17
  k change web "Initial Set nginx"
  k set image deployment/web nginx=nginx:1.19
  kchange web "Update nginx to 1.19" #tuto : kchange <deployment> <message>
  k rollout history deployment web
```
