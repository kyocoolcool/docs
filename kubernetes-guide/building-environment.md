# Building Environment

🎯 Create Compute Engine x 4, which includes control plane x 1, worker node x3.

{% tabs %}
{% tab title="ssh to master" %}
```bash
gcloud compute ssh --zone "asia-east1-b" "master" --project "civic-karma-321707"
```
{% endtab %}

{% tab title="ssh to worker1" %}
```bash
gcloud compute ssh --zone "asia-east1-b" "worker1" --project "civic-karma-321707
```
{% endtab %}

{% tab title="ssh to worker2" %}
```bash
gcloud compute ssh --zone "asia-east1-b" "worker2" --project "civic-karma-321707
```
{% endtab %}

{% tab title="ssh to worker3" %}
```bash
gcloud compute ssh --zone "asia-east1-b" "worker3" --project "civic-karma-321707
```
{% endtab %}
{% endtabs %}

🎯 Close swap

```text
swapoff -a
sudo sed '/swap/d' -i /etc/fstab
```

🎯 Close Firewall

```bash
systemctl disable firewalld
systemctl stop firewalld
```

🎯 Close SELinux

```bash
setenforce 0
sed -i 's/enforcing/disabled/' /etc/selinux/config
```

🎯 Setting kubernetes repository

```bash
vi /etc/yum.repos.d/kubernetes.repo
```

{% code title="kubernetes.repo" %}
```bash
[kubernetes]
name=Kubernetes Repository
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
```
{% endcode %}

🎯 Install Docker

```bash
yum install docker
```

🎯 Install Kubeadm, Kubelet, Kubectl

{% hint style="info" %}
🧙♂ worker node didn't need install 
{% endhint %}

```bash
yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
```

🎯 Start Docker

```bash
systemctl enable docker && systemctl start docker
```

🎯 Start Kubelet

```bash
systemctl enable kubelet && systemctl start kubelet
```

🎯 Kubernetes component image pull

```bash
kubeadm config images pull
```

🎯 Kubernetes master initial

```bash
kubeadm init
```

🎯 End of initial will display below result

```bash
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.140.0.3:6443 --token pzfri9.g6ecr31em3gja1hm \
	--discovery-token-ca-cert-hash sha256:d05fbac9086e6067d1115c4170bd0763e4deba72cd8b5ef01072f47cac9d72f7
```

🎯 Step by above display

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

🎯 Worker node create join config file

{% code title="join-config.yaml" %}
```bash
apiVersion: kubeadm.k8s.io/v1beta3
kind: JoinConfiguration
discovery:
  bootstrapToken:
    apiServerEndpoint: 10.140.0.3:6443
    token: pzfri9.g6ecr31em3gja1hm
    unsafeSkipCAVerification: true
  tlsBootstrapToken: pzfri9.g6ecr31em3gja1hm
```
{% endcode %}

🎯 Worker node join cluster

```bash
kubeadm join --config=join-config.yaml
```



