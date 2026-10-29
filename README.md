# iaccarino_rl26
 
Ambiente di sviluppo **ROS 2 Humble + Gazebo Fortress** in Docker per Robotics Lab 2026 (Classwork 0).
 
## Struttura
```
iaccarino_rl26/
|- docker_scripts/   # Dockerfile, docker_build_image.sh, docker_run_container.sh, docker_connect.sh
|- src/              # montata nel container come /home/user/ros2_ws/src
|- README.md
```
 
## Prerequisiti
Linux (Ubuntu 22.04 nativo, WSL2 o VM), Git e Docker. Se Docker non è installato:
```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg git
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo groupadd docker 2>/dev/null; sudo usermod -aG docker $USER
```
Poi fare logout e login.
 
## Da macchina pulita a container in esecuzione
```bash
git clone https://github.com/<username>/iaccarino_rl26.git
cd iaccarino_rl26/docker_scripts
chmod +x *.sh
 
# 1. Build dell'immagine (da eseguire DENTRO docker_scripts, dove c'è il Dockerfile)
./docker_build_image.sh ros2_docker
 
# 2. Avvio del container (terminale 1), dalla root del progetto
cd ..
./docker_scripts/docker_run_container.sh ros2_docker rl26_cont iaccarino_rl26/src
 
# 3. Secondo terminale nello stesso container (terminale 2)
./docker_scripts/docker_connect.sh rl26_cont
```
L'ultimo argomento di `docker_run_container.sh` è relativo alla home: `iaccarino_rl26/src`
(cioè `~/iaccarino_rl26/src`) viene montata in `/home/user/ros2_ws/src`.
Il container viene eliminato automaticamente all'uscita (`--rm`). Password sudo nel container: `user`.
 
## Verifica
Dentro il container:
```bash
echo $ROS_DISTRO        # atteso: humble
ros2 pkg list           # elenco dei pacchetti installati
touch /home/user/ros2_ws/src/test_persistence.txt
exit
```
Sull'host, dopo l'uscita dal container:
```bash
docker ps -a            # nessun container rimasto
ls src/                 # test_persistence.txt è ancora presente: il codice persiste
```
# iaccapaolo
