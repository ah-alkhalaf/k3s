# K3s Installation Guide

## Introduction

K3s is a lightweight Kubernetes distribution designed specifically for edge computing and IoT applications. This guide describes how to install K3s with one master node and two worker nodes. It also covers common issues and their solutions.

## Requirements.

- **3 Linux servers** (e.g., debian-12.6.0-amd64): one for the master node and two for the worker nodes.
- **Root access** on all servers.
- **Internet access** to install dependencies and K3s.

**Note:** The host names do not have to be identical. If you want to change the hostname later, use the following command:

```bash
sudo hostnamectl set-hostname <new-hostname>
```

## Step 1: Install the master Node

1. Login to the master node and install K3s:

   ```bash
   curl -sfL https://get.k3s.io | sh -
   ```

2. Note the token for the worker nodes. The token can be found in the file `/var/lib/rancher/k3s/server/node-token`:

   ```bash
   sudo cat /var/lib/rancher/k3s/server/node-token
   ```

   **Note:** Adjust the IP address in the `/etc/rancher/k3s/k3s.yaml` file:

   ```bash
   vim /etc/rancher/k3s/k3s.yaml
   ```

3. Verify that K3s has been successfully installed:

   ```bash
   sudo systemctl status k3s
   ```

## Step 2: Install worker nodes

1. Login to the first worker node. Install K3s and connect it to the master node. Replace `<MASTER_IP>` with the IP address of the master node and `<NODE_TOKEN>` with the token noted in the previous step:

   ```bash
   curl -sfL https://get.k3s.io | K3S_URL=https://<MASTER_IP>:6443 K3S_TOKEN=<NODE_TOKEN> sh -
   ```

2. Check the status of the K3s service:

   ```bash
   sudo systemctl status k3s
   ```

3. Repeat step 1 for the second worker node.

## Step 3: Verify the installation

1. On the master node, run the following command to check the status of the nodes:

   ```bash
   sudo k3s kubectl get nodes
   ```

2. You should see both worker nodes as well as the master node in the list.

## Troubleshooting

If you encounter problems, check the logs of the K3s service:

```bash
sudo journalctl -u k3s
```

- Make sure all required ports are open (6443, 10250, 10255):

```bash
nc -zv <IP_ADDRESS> 6443
```

