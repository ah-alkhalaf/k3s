# K3s Installationsanleitung

## Einführung

K3s ist eine leichtgewichtige Kubernetes-Distribution, die speziell für Edge-Computing und IoT-Anwendungen entwickelt wurde. Diese Anleitung beschreibt die Installation von K3s mit einem Master-Node und zwei Worker-Nodes. Außerdem werden häufige Probleme und deren Lösungen behandelt.

## Voraussetzungen

- **3 Linux-Server** (z. B. debian-12.6.0-amd64): einer für den Master-Node und zwei für die Worker-Nodes.
- **Root-Zugriff** auf allen Servern.
- **Internetzugang** für die Installation von Abhängigkeiten und K3s.

**Hinweis:** Die Hostnamen müssen nicht identisch sein. Wenn du den Hostnamen später ändern möchtest, verwende den folgenden Befehl:

```bash
sudo hostnamectl set-hostname <neuer-hostname>
```

## Schritt 1: Master-Node installieren

1. Melde dich am Master-Node an und installiere K3s:

   ```bash
   curl -sfL https://get.k3s.io | sh -
   ```

2. Notiere den Token für die Worker-Nodes. Der Token befindet sich in der Datei `/var/lib/rancher/k3s/server/node-token`:

   ```bash
   sudo cat /var/lib/rancher/k3s/server/node-token
   ```

   **Hinweis:** Passe die IP-Adresse in der Datei `/etc/rancher/k3s/k3s.yaml` an:

   ```bash
   vim /etc/rancher/k3s/k3s.yaml
   ```

3. Überprüfe, ob K3s erfolgreich installiert wurde:

   ```bash
   sudo systemctl status k3s
   ```

## Schritt 2: Worker-Node installieren

1. Melde dich am ersten Worker-Node an. Installiere K3s und verbinde dich mit dem Master-Node. Ersetze `<MASTER_IP>` mit der IP-Adresse des Master-Nodes und `<NODE_TOKEN>` mit dem Token, den du im vorherigen Schritt notiert hast:

   ```bash
   curl -sfL https://get.k3s.io | K3S_URL=https://<MASTER_IP>:6443 K3S_TOKEN=<NODE_TOKEN> sh -
   ```

2. Überprüfe den Status des K3s-Dienstes:

   ```bash
   sudo systemctl status k3s
   ```

3. Wiederhole Schritt 1 für den zweiten Worker-Node.

## Schritt 3: Überprüfen der Installation

1. Auf dem Master-Node führe den folgenden Befehl aus, um den Status der Nodes zu überprüfen:

   ```bash
   sudo k3s kubectl get nodes
   ```

2. Du solltest beide Worker-Nodes sowie den Master-Node in der Liste sehen.

## Fehlerbehebung

Falls Probleme auftreten, überprüfe die Logs des K3s-Dienstes:

```bash
sudo journalctl -u k3s
```

- Stelle sicher, dass alle erforderlichen Ports offen sind (6443, 10250, 10255):

```bash
nc -zv <IP_ADDRESS> 6443
```

