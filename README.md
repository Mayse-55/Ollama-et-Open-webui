## Installation LLM : OLLAMA + Open-webui

### 📦 Prérequis

- Distribution **Debian** ou **Ubuntu** à jour  
- Carte graphique **NVIDIA** avec pilotes propriétaires déjà installés  
- Accès administrateur avec **sudo**  
- Accès SSH au serveur via **OpenSSH** ou autre *(facultatif)*

## Installation de Ollama

**1 - Mettre à jour le système**
```bash
sudo apt update 
```
```bash 
sudo apt dist-upgrade -y 
```
**2 - Installer les outils de base**
```bash
sudo apt install -y curl gpg ; apt update
```
**3 - Ajouter le dépôt NVIDIA Container Toolkit**
```bash
sudo curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```
**4 - Ajouter la source logicielle**
```bash
sudo sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list
```
**5 - Mettre a jour les dépôt**
```bash
sudo apt update 
```
**6 - Corriger les dépôts APT en double**

**⚠️ Attention il se peut qu'au début, lors de l'ajout des dépôts NVIDIA, vous ayez en fesant** ```apt update``` **ces avertissements :** ```W: La cible Translations ...```

![Capture d'écran 2025-05-14 081324](https://github.com/user-attachments/assets/6ce65a9a-12b6-4481-9f1b-2729891c0479)
**a - Supprimez le fichier en double.** 
```bash
sudo rm /etc/apt/sources.list.d/contrib.list
```
**b - Vérifiez si les avertissements sont toujours présents.**
```bash
apt update
```

**📌 Si vous n'avez plus d'avertissements, le problème est résolu.**

**7 - Installer NVIDIA Container toolkit**
```bash
sudo apt-get install -y nvidia-container-toolkit
```
**8 - Installer OLLAMA**
```bash
sudo curl -fsSL https://ollama.com/install.sh | sh
```
![Capture d'écran 2025-05-13 182238](https://github.com/user-attachments/assets/4b5092e7-d30b-4dcf-90ce-4f1a2c371886)
```bash
sudo reboot
```
**⚠️ Attention si a la fin il y a « WARNING: No NVIDIA/AMD GPU detected. Ollama will run in CPU-only mode. » la carte graphique n’est pas détecter**

![Capture d'écran 2025-05-13 181942](https://github.com/user-attachments/assets/f5f502e7-9ab3-42c1-96ef-7e001a2611bc)

**9 - Installer monitoring nvidia « nvtop »**
```bash
sudo apt install nvtop 
```
```bash
sudo nvtop
```
**📌 Nvtop permet de visionner l’utilisation du GPU**

**10 - Tester OLLAMA**
```bash
sudo ollama run mistral
```
![Capture d'écran 2025-05-13 181838](https://github.com/user-attachments/assets/56b60ba3-6aea-4342-88d8-db396e0fd454)

-------------------------------------------------------------

## **Installation de Open-webui :**

**1 - Mettre a jour les dépôt**
```bash
sudo apt update
```
**2 - Installer docker docker-compose**
```bash 
sudo apt install docker docker-compose
```
**3 - Installer Open-webui dans docker**
```bash
sudo docker run -d -p 3000:8080 \
  --gpus all \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:cuda
```
**⚠️ Attention ceci peut prendre beaucoup de temps**

**4 - Configurer Systemd**
```bash
sudo nano /etc/systemd/system/ollama.service
```
**Ajouter ceci et Enregistrer :**
```bash
Environment="PATH=$PATH"
Environment="OLLAMA_HOST=0.0.0.0"
```
**5 - Lancer Ollama**
```bash
sudo systemctl deamon-reload
```
```bash
sudo systemctl enable ollama.service
```
```bash
sudo systemctl restart ollama.service
```
**6 - Accéder a l’interface web open-webui + monitoring nvtop**
```bash
http://ip-serveur:3000
```

![Capture d'écran 2025-05-13 203520](https://github.com/user-attachments/assets/d2587bdb-47ac-4581-b1c5-dcf620340d5e)

**Lancer nvtop sur le serveur :**
```bash
sudo nvtop
```
![Capture d'écran 2025-05-13 203645](https://github.com/user-attachments/assets/e28de005-f8d2-494e-921e-93bd68a61ccb)

**7 - Pour ajouter des LLM sur open-webui**

![demo](https://github.com/user-attachments/assets/b774f670-126f-4b1d-954d-a550e9b60d41)

-------------------------------------------------------------

# Mises à jour

![Capture d'écran 2025-05-13 232726](https://github.com/user-attachments/assets/067a8b69-225c-438f-bac7-c0dff350ae2c)

**1 - Arrêter et supprimer l’ancien conteneur (pas le volume)**
```bash
sudo docker stop open-webui
```
```bash
sudo docker rm open-webui
```
**2 - Mettre à jour l’image**
```bash
sudo docker pull ghcr.io/open-webui/open-webui:cuda
```
**3 - Relancer avec la même commande**
```bash
sudo docker run -d -p 3000:8080 \
  --gpus all \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:cuda
```
