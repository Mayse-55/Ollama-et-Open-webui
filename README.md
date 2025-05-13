Installation LLM : OLLAMA + OPENWEB UI 

Guide d'installation complet :

Préqueris :

- Distribution Debian ou Ubuntu à jour 
- Carte graphique NVIDIA + pilotes propriétaires déjà installés 
- Accès administrateur (sudo) 
- Accès SSH au serveur via openssh ou autre « facultatif »

1. Mettre à jour le système
```bash
sudo apt update 
```
```sudo apt dist-upgrade -y ```

2. Installer les outils de base
# sudo apt install -y curl gpg ; apt update

3. Ajouter le dépôt NVIDIA Container Toolkit
# curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

4.  Ajouter la source logicielle
# sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list

5. Mettre a jour les dépôt
# apt update 

6. Installer NVIDIA Container toolkit
# apt-get install -y nvidia-container-toolkit

8. Installer OLLAMA
# curl -fsSL https://ollama.com/install.sh | sh

# reboot

⚠️ Par contre si a la fin il y a « WARNING: No NVIDIA/AMD GPU detected. Ollama will run in CPU-only mode. » la carte graphique n’est pas détecter a vous de régler se problème

7. Installer monitoring nvidia « nvtop »
# apt install nvtop 
# nvtop

Nvtop permet de visionner l’utilisation du GPU

9. Tester OLLAMA 
# ollama run mistral

Deuxième parti installation d’Open-webui :

1. Mettre a jour les dépôt
# apt update

2. Installer docker docker-compose
# apt install docker docker-compose

3. Installer Open-webui dans docker
# docker run -d -p 3000:8080 --gpus all --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:cuda

⚠️ Attention ceci peut prendre beaucoup de temp

4. Configurer Systemd 
# nano /etc/systemd/system/ollama.service

Ajouter ceci et Enregistrer : 

Environment="PATH=$PATH"
Environment="OLLAMA_HOST=0.0.0.0"

5. Lancer Ollama 
# systemctl deamon-reload
# systemctl enable ollama.service
# systemctl start ollama.service

6. Tester OLLAMA 
# ollama run mistral 

7. Accéder a l’interface web open-webui + monitoring nvtop 
http://ip-serveur:3000

Sur le serveur :
# nvtop





Documentation effectué par Nathaël Polnecq
