
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

4. **Join Worker Nodes to Cluster:**

   * On each worker node (slave nodes), run:

     * `kubeadm reset` to clear any previous setup.
     * Join the cluster using the token generated on the master, something like:

       ```
       kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
       ```
   * Switch to root user if necessary before running these commands.

5. **Verify Cluster Nodes:**

   * On master node, run:

     ```
     kubectl get nodes
     ```
   * Confirm that all master and worker nodes show as `Ready`.

6. **Docker Installation (on all nodes):**

   * Check for any held Kubernetes packages (`kubeadm`, `kubectl`, `kubelet`) with:

     ```
     dpkg --get-selections | grep hold
     ```
   * Unhold these packages if needed:

     ```
     sudo apt-mark unhold <package-name>
     ```
   * Remove conflicting `containerd` packages if necessary:

     ```
     sudo apt remove --purge containerd
     ```
   * Update system and install `containerd.io` (the right version).
   * Install `docker.io` afterward.
   * Confirm Docker installation with:

     ```
     docker version
     ```
   * Repeat the Docker installation steps on all worker nodes.

7. **Optional:**

   * The `kubeadm reset` (preflight check) is not mandatory but recommended to avoid errors.

---

### Additional Tips

* Run installation commands step-by-step instead of bulk copying.
* Always check package hold status especially after kubeadm/kubelet installs.
* Sharing your full setup scripts or documentation at the end of the session is a great idea for reproducibility.
* After the break, you plan to cover deployment YAMLs and exposing services — looking forward to that!

---

If you want, I can help you convert these notes into a clean step-by-step guide, script them in Bash, or prepare example Kubernetes deployment and service YAMLs next. Just let me know! Also, feel free to ask if you want to clarify any specific command or step.

---
