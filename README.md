# Install, Setup and Overview kubectl
## Install kubectl on Linux
#### Install kubectl binary with curl on Linux
1. Download the latest release with the command, for example, to download version v1.18.0 on Linux, type:
```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.18.0/bin/linux/amd64/kubectl
```
2. Make the kubectl binary executable.
```bash
chmod +x ./kubectl
```
3. Move the binary in to your PATH.
```bash
sudo mv ./kubectl /usr/local/bin/kubectl
```
4. Test to ensure the version you installed is up-to-date:
```bash
kubectl version --client
```
#### OR Install it using native package management
```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

## Setup kubectl on Linux
#### Verifying kubectl configuration
  - In order for kubectl to find and access a Kubernetes cluster, it needs a kubeconfig file, which is created automatically when you create a cluster using  a [Kind](https://github.com/genral73/k8s-kind) or [Minikube](https://github.com/genral73/k8s-minikube) clusters.
  - By default, kubectl configuration is located at ~/.kube/config.

1. Check that kubectl is properly configured by getting the cluster state:
```bash
kubectl cluster-info
```
- If you see a message similar to the following, kubectl is not configured correctly or is not able to connect to a Kubernetes cluster.

>   output: The connection to the server <server-name:port> was refused - did you specify the right host or port?
<hr/>

#### Enabling shell autocompletion
##### The completion script depends on bash-completion, which means that you have to install this software first:
1. Install bash-completion
```bash
apt-get install bash-completion
```
2. if the command succeeds, you’re already set, otherwise add the following to your ~/.bashrc file:
```
source /usr/share/bash-completion/bash_completion
```
3. Reload your shell and verify that bash-completion is correctly installed by typing type:
```bash
type _init_completion
```

##### You now need to ensure that the kubectl completion script gets sourced in all your shell sessions:
1. Source the completion script in your ~/.bashrc file:
```bash
echo 'source <(kubectl completion bash)' >>~/.bashrc
```
2. If you have an alias for kubectl, you can extend shell completion to work with that alias:
```bash
echo 'alias k=kubectl' >>~/.bashrc
echo 'complete -F __start_kubectl k' >>~/.bashrc
```
<img src="images/autocompletion.gif" />
<hr/>

## Overview of kubectl
This overview covers kubectl syntax, describes the command operations, and provides common example.
For installation instructions see [installing kubectl](https://github.com/genral73/k8s-cli#install-kubectl-on-linux).
- Syntax
- Operations
- Resource types
- Output Formatting options
- Examples: Common operations

<br/>

#### Syntax
1. Use the following syntax to run kubectl commands from your terminal window:
```bash
kubectl [command] [TYPE] [NAME] [flags]
```
where `command`, `TYPE`, `NAME`, and `flags` are:
- command: Specifies the operation that you want to perform on resources, for example `create`, `get`, `describe`, `delete`.
- TYPE: Specifies the resource type, and it are case-insensitive, for exmaple `pods`, `configmaps`, `services`, `nodes`.
- NAME: Specifies the name of the resource, and it are case-sensitive, for exmaple `my-nginx-pod`, `dev-apache-pod`.
- flags: Specifies optional flags. For example, you can use the -s or --server flags to specify the address and port of the Kubernetes API serve
> If you need help, just run kubectl help from the terminal window.

<br/>

#### Operations:
For more about command operations, see the [kubectl](https://kubernetes.io/docs/user-guide/kubectl) reference documentation.
The following table includes  most popular operations:
<img src="images/Operations.png" />

<br/>

#### Resources:
For more about command resources, see the [kubectl](https://kubernetes.io/docs/user-guide/kubectl) reference documentation.
The following table includes  most popular resources:
<img src="images/Resources.png" />

#### Output Formatting options
Use the following sections for information about how you can format or sort the output of certain commands.
The default output format for all kubectl commands is the human readable plain-text format, you can add either the -o or --output flags like:
```bash
kubectl [command] [TYPE] [NAME] -o <output_format>
```
Examples:
```bash
kubectl get pod web-pod-13je7 -o yaml
kubectl get pod web-pod-13je7 -o custom-columns=NAME:.metadata.name,RSRC:.metadata.resourceVersion
kubectl get pod web-pod-13je7 -o yaml 
kubectl get pods --sort-by=.metadata.name
```

<br/>

#### Examples: Common operations
1. kubectl get - List one or more resources
```bash
# List all pods in plain-text output format.
kubectl get pods

# List all pods in plain-text output format and include additional information (such as node name).
kubectl get pods -o wide

# List the replication controller with the specified name in plain-text output format. Tip: You can shorten and replace the 'replicationcontroller' resource type with the alias 'rc'.
kubectl get replicationcontroller <rc-name>

# List all replication controllers and services together in plain-text output format.
kubectl get rc,services

# List all daemon sets in plain-text output format.
kubectl get ds

# List all pods running on node server01
kubectl get pods --field-selector=spec.nodeName=server01
```

2. kubectl describe - Display detailed state of one or more resources, including the uninitialized ones by default.
```bash
# Display the details of the node with name <node-name>.
kubectl describe nodes <node-name>

# Display the details of the pod with name <pod-name>.
kubectl describe pods/<pod-name>

# Display the details of all the pods that are managed by the replication controller named <rc-name>.
# Remember: Any pods that are created by the replication controller get prefixed with the name of the replication controller.
kubectl describe pods <rc-name>

# Describe all pods
kubectl describe pods
```

3. kubectl delete - Delete resources either from a file, stdin, or specifying label selectors, names, resource selectors, or resources.
```bash
# Delete a pod using the type and name specified in the pod.yaml file.
kubectl delete -f pod.yaml

# Delete all the pods and services that have the label '<label-key>=<label-value>'.
kubectl delete pods,services -l <label-key>=<label-value>

# Delete all pods, including uninitialized ones.
kubectl delete pods --all
```

4. kubectl exec - Execute a command against a container in a pod.
```bash
# Get output from running 'date' from pod <pod-name>. By default, output is from the first container.
kubectl exec <pod-name> -- date

# Get output from running 'date' in container <container-name> of pod <pod-name>.
kubectl exec <pod-name> -c <container-name> -- date

# Get an interactive TTY and run /bin/bash from pod <pod-name>. By default, output is from the first container.
kubectl exec -ti <pod-name> -- /bin/bash
```

5. kubectl logs - Print the logs for a container in a pod.
```bash
# Return a snapshot of the logs from pod <pod-name>.
kubectl logs <pod-name>

# Start streaming the logs from pod <pod-name>. This is similar to the 'tail -f' Linux command.
kubectl logs -f <pod-name>
```

6. kubectl diff - View a diff of the proposed updates to a cluster.
```bash
# Diff resources included in "pod.json".
kubectl diff -f pod.json

# Diff file read from stdin.
cat service.yaml | kubectl diff -f -
```

<br/>
#### Additional Tips
1. Install zsh and Oh-My-Zsh
```bash
sudo apt install zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
echo 'source <(kubectl completion zsh)' >>~/.zshrc
echo 'alias k=kubectl' >>~/.zshrc
echo 'complete -F __start_kubectl k' >>~/.zshrc
```
2. Install kubectx + kubens: Power tools for kubectl
```bash
sudo apt install kubectx
echo 'alias kns=kubens' >>~/.zshrc
```
3. Install kube-ps1: Kubernetes prompt for bash and zsh
```bash
wget https://raw.githubusercontent.com/jonmosco/kube-ps1/master/kube-ps1.sh -O ~/.kube-ps1.sh
echo '~/.kube-ps1.sh' >> ~/.zshrc
echo "PROMPT='$(kube_ps1)'$PROMPT" >> ~/.zshrc
```

#### What's next
 Create a k8s cluster on your laptop by use any one of following:
 1. [Kind Cluster](https://github.com/genral73/k8s-kind).
 2. [Minikube Cluster](https://github.com/genral73/k8s-minikube).
