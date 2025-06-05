
### Kubernetes Cluster Setup Summary

1. **Initialize Kubernetes Master Node:**

## Execute ONLY on "Master Node"

```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

mkdir -p $HOME/ .kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/ .kube/config
sudo chown $(id-u):$(id -g) $HOME/ .kube/config

#Network plugin
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

## Execute on ALL of your Worker Node's

1. Perform pre-flight checks

```
   sudo kubeadm reset pre-flight checks
```

2. Paste the join command you got from the master node and append `--v=5` at the end but first use sudo su command to become root (avoid using sudo your-token).
   
```
   sudo su
   <your-token --v=5>
```


## Verify Cluster Connection

**On Master Node:**
```
kubectl get nodes
```

---

6. **Docker Installation (on all nodes):**

# 🐳 Project 2 – Fixing `containerd.io` Conflicts While Installing Docker

If you're facing errors related to `containerd.io` during Docker installation, it usually means there's a conflict with the default `containerd` package or there are held packages on your system.

Follow these steps to resolve the issue:

---

## ✅ Step-by-Step Solution

### 1️⃣ Check for Held Packages

Held packages may block new installations or upgrades.

```bash
dpkg --get-selections | grep hold
```

If any held packages appear, unhold them:

```bash
sudo apt-mark unhold <package-name>
```

---

### 2️⃣ Remove Conflicting `containerd`

The default `containerd` may conflict with `containerd.io`. Remove it first:

```bash
sudo apt-get remove --purge containerd
```

---

### 3️⃣ Clean Up and Update APT

Clean leftover dependencies and update your package list:

```bash
sudo apt-get autoremove
sudo apt-get autoclean
sudo apt-get update
```

---

### 4️⃣ Install `containerd.io`

Now proceed with a clean installation:

```bash
sudo apt-get install containerd.io
```

---

### 6️⃣ Install Docker Engine

Once containerd is in place, install Docker:

```bash
sudo apt-get install docker-ce docker-ce-cli
```

> Make sure your Docker CE version is compatible with your system and `containerd.io`.

---

### 🔍 Bonus: Check Docker and Containerd Versions

Verify installed versions to ensure compatibility:

```bash
docker --version
containerd --version
```

---

