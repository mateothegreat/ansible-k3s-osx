# Kubernetes on OSX via multipass

```bash
(.venv) ~/workspace/ansible-k3s-osx ➜ kubectx
Switched to context "default".
(.venv) ~/workspace/ansible-k3s-osx ➜ kubectl get node -o wide
NAME       STATUS   ROLES                  AGE   VERSION        INTERNAL-IP      EXTERNAL-IP   OS-IMAGE           KERNEL-VERSION     CONTAINER-RUNTIME
master     Ready    control-plane,master   49m   v1.29.6+k3s2   192.168.205.11   <none>        Ubuntu 24.04 LTS   6.8.0-36-generic   containerd://1.7.17-k3s1
worker-1   Ready    <none>                 37m   v1.29.6+k3s2   192.168.205.12   <none>        Ubuntu 24.04 LTS   6.8.0-36-generic   containerd://1.7.17-k3s1
worker-2   Ready    <none>                 37m   v1.29.6+k3s2   192.168.205.13   <none>        Ubuntu 24.04 LTS   6.8.0-36-generic   containerd://1.7.17-k3s1
(.venv) ~/workspace/ansible-k3s-osx ➜ kubectl get pod -A      
NAMESPACE     NAME                                     READY   STATUS      RESTARTS   AGE
kube-system   coredns-6799fbcd5-nd2sn                  1/1     Running     0          49m
kube-system   helm-install-traefik-96g5m               0/1     Completed   1          49m
kube-system   helm-install-traefik-crd-mmtfl           0/1     Completed   0          49m
kube-system   local-path-provisioner-6f5d79df6-zgqzj   1/1     Running     0          49m
kube-system   metrics-server-54fd9b65b-rsxqq           1/1     Running     0          49m
kube-system   svclb-traefik-447751df-d9r8w             2/2     Running     0          37m
kube-system   svclb-traefik-447751df-lnfb7             2/2     Running     0          37m
kube-system   svclb-traefik-447751df-n6rvm             2/2     Running     0          48m
kube-system   traefik-7d5f6474df-fzsdt                 1/1     Running     0          48m
```

```bash
(.venv) ~/workspace/ansible-k3s-osx/test ➜ multipass list
Name                    State             IPv4             Image
master                  Running           192.168.205.11   Ubuntu 24.04 LTS
                                          10.42.0.0
                                          10.42.0.1
worker-1                Running           192.168.205.12   Ubuntu 24.04 LTS
                                          10.42.1.0
                                          10.42.1.1
worker-2                Running           192.168.205.13   Ubuntu 24.04 LTS
                                          10.42.2.0
                                          10.42.2.1
```

## Usage

```bash
ansible-playbook test/playbook.yaml
```

`test/playbook.yaml`:

```yaml
- hosts: localhost
  roles:
    - role: ../role
      vars:
        kubeconfig: true
        nodes:
          - name: "master"
            type: "master"
            cpus: 1
            memory: 2G
            disk: 20G
          - name: "worker-1"
            type: "worker"
            cpus: 4
            memory: 8G
            disk: 20G
          - name: "worker-2"
            type: "worker"
            cpus: 4
            memory: 8G
            disk: 20G
```

### Copy kubeconfig to local

If you need to copy kubeconfig to local, you can use `--tags kube-config`:

> **Note**: This is already performed during the main playbook run when `kubeconfig: true`.

```bash
ansible-playbook test/playbook.yaml --tags kube-config
```
