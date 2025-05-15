# 🧠 Installation LLM : OLLAMA + Open-WebUI

## 📦 Prérequis

* Système à jour : **Debian** ou **Ubuntu**
* Carte graphique **NVIDIA** avec pilotes propriétaires installés
* Accès administrateur (**sudo**)
* Accès SSH au serveur via **OpenSSH** *(optionnel)*

---

## ⚙️ Installation de Ollama

### 1️⃣ Mettre à jour le système

```bash
sudo apt update && sudo apt dist-upgrade -y
```

### 2️⃣ Installer les outils de base

```bash
sudo apt install -y curl gpg && sudo apt update
```

### 3️⃣ Ajouter le dépôt NVIDIA Container Toolkit

```bash
sudo curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | \
  sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg && \
  curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
  sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
  sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

### 4️⃣ Décommenter la ligne "experimental" (si présente)

```bash
sudo sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

### 5️⃣ Mettre à jour les dépôts

```bash
sudo apt update
```

### ⚠️ Supprimer les dépôts en double (si message d’erreur)

```bash
sudo rm /etc/apt/sources.list.d/contrib.list
sudo apt update
```

### 6️⃣ Installer NVIDIA Container Toolkit

```bash
sudo apt install -y nvidia-container-toolkit
```

### 7️⃣ Installer Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### 8️⃣ Redémarrer le serveur

```bash
sudo reboot
```

⚠️ Si vous voyez `WARNING: No NVIDIA/AMD GPU detected`, la carte graphique n’est pas détectée.

### 9️⃣ Installer le moniteur GPU (nvtop)

```bash
sudo apt install nvtop && sudo nvtop
```

### 🔟 Tester Ollama avec Mistral

```bash
ollama run mistral
```

---

## 🌐 Installation de Open-WebUI

### 1️⃣ Mise à jour des paquets

```bash
sudo apt update
```

### 2️⃣ Installer Docker et Docker Compose

```bash
sudo apt install docker docker-compose
```

### 3️⃣ Lancer Open-WebUI via Docker

```bash
sudo docker run -d -p 3000:8080 \
  --gpus all \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:cuda
```

⚠️ Cela peut prendre un certain temps

### 4️⃣ Configurer systemd pour Ollama

```bash
sudo nano /etc/systemd/system/ollama.service
```

Ajouter :

```ini
[Service]
Environment="PATH=$PATH"
Environment="OLLAMA_HOST=0.0.0.0"
```

### 5️⃣ Lancer Ollama avec systemd

```bash
sudo systemctl daemon-reexec
sudo systemctl enable ollama.service
sudo systemctl restart ollama.service
```

### 6️⃣ Accéder à l'interface WebUI

Depuis un navigateur :

```
http://<IP-DU-SERVEUR>:3000
```

Lancer également le moniteur GPU :

```bash
sudo nvtop
```

### 7️⃣ Ajouter des modèles dans Open-WebUI

![demo](https://github.com/user-attachments/assets/14220a3a-0ee0-4892-a604-4e4b4d97c347)

---

## 🔄 Mise à jour d’Open-WebUI

### 1️⃣ Stopper et supprimer l'ancien conteneur

```bash
sudo docker stop open-webui
sudo docker rm open-webui
```

### 2️⃣ Mettre à jour l'image

```bash
sudo docker pull ghcr.io/open-webui/open-webui:cuda
```

### 3️⃣ Relancer le conteneur

```bash
sudo docker run -d -p 3000:8080 \
  --gpus all \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:cuda
```
