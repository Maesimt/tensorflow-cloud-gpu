# Start a vm in the cloud

# Installation with Docker

source:
- https://www.tensorflow.org/install/docker?hl=fr
- https://collabnix.com/introducing-new-docker-cli-api-support-for-nvidia-gpus-under-docker-engine-19-03-0-beta-release/

1. Verifier que la carte Nvidia est detecter.
```console
tensorflow-gpu:~$ lspci -vv | grep -i nvidia
00:04.0 3D controller: NVIDIA Corporation GP100GL [Tesla P100 PCIe 16GB] (rev a1)
        Subsystem: NVIDIA Corporation GP100GL [Tesla P100 PCIe 16GB]
        Kernel modules: nvidiafb
```

2. Install the Nvidia drivers.
```
apt-get install ubuntu-drivers-common && sudo ubuntu-drivers autoinstall -y
```
Restart
```
sudo shutdown -r now
```

3. Install Nvidia COntainer Runtime

Creer un fichier vide
```
touch nvidia-container-runtime-script.sh
```
```
nano nvidia-container-runtime-script.sh
```
Mettre ca dedans
```
curl -s -L https://nvidia.github.io/nvidia-container-runtime/gpgkey | \
  sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-container-runtime/$distribution/nvidia-container-runtime.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-container-runtime.list
sudo apt-get update
```
Executer le script
```
sh nvidia-container-runtime-script.sh
```

Installer le runtime
```
apt-get install nvidia-container-runtime -y
```

Regarder si le hook est configurer
```
tensorflow-gpu:~$ which nvidia-container-runtime-hook
/usr/bin/nvidia-container-runtime-hook
```

3. Docker installation
Using the convient script
```
tensorflow-gpu@demo:~$ curl -fsSL https://get.docker.com -o get-docker.sh
tensorflow-gpu@demo:~$ sudo sh get-docker.sh
````
Add a non-root user (takes a restart to take effect)
```
tensorflow-gpu@demo:~$ sudo usermod -aG docker cummings_guillaume
```
Verifier l'installation
```
sudo docker version
```

Verifier l'option gpu pour docker run
```
tensorflow-gpu:~$ docker run --help | grep -i gpus
      --gpus gpu-request               GPU devices to add to the container ('all' to pass all GPUs)
```

Tester un container pour voir le panneau Nvidia-smi avec l'option `--gpus all`:
```
cummings_guillaume@tensorflow-gpu:~$ sudo docker run -it --rm --gpus all ubuntu nvidia-smi
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
6a5697faee43: Pull complete 
ba13d3bc422b: Pull complete 
a254829d9e55: Pull complete 
Digest: sha256:fff16eea1a8ae92867721d90c59a75652ea66d29c05294e6e2f898704bdb8cf1
Status: Downloaded newer image for ubuntu:latest
Sat Nov 14 15:16:52 2020       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 450.80.02    Driver Version: 450.80.02    CUDA Version: N/A      |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla P100-PCIE...  Off  | 00000000:00:04.0 Off |                    0 |
| N/A   37C    P0    27W / 250W |      0MiB / 16280MiB |      4%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```
Verifier si docker voit les bonnes informations de cartes:
```
cummings_guillaume@tensorflow-gpu:~$ sudo docker run -it --rm --gpus all ubuntu nvidia-smi -L
GPU 0: Tesla P100-PCIE-16GB (UUID: GPU-81c18f90-dd00-61d7-7581-120bc6f10cf0)
```

Lancer l'image de tensorflow avec l'option gpu en mode detacher et exposer le port.
```
sudo docker run -it -d -p 8888:8888 --gpus all tensorflow/tensorflow:1.15.4-gpu-py3-jupyter
```
Recuperer l'id du container
```console
cummings_guillaume@tensorflow-gpu:~$ sudo docker ps
CONTAINER ID        IMAGE                                          COMMAND                  CREATED             STATUS              PORTS                    NAMES
964b9789b1b1        tensorflow/tensorflow:1.15.4-gpu-py3-jupyter   "bash -c 'source /et…"   5 seconds ago       Up 4 seconds        0.0.0.0:8888->8888/tcp   hopeful_per
lman
```

Se connecter sur la machine, installer git et cloner le repo du cours.
```console
cummings_guillaume@tensorflow-gpu:~$ sudo docker exec -it 964b9789b1b1 /bin/bash
________                               _______________                
___  __/__________________________________  ____/__  /________      __
__  /  _  _ \_  __ \_  ___/  __ \_  ___/_  /_   __  /_  __ \_ | /| / /
_  /   /  __/  / / /(__  )/ /_/ /  /   _  __/   _  / / /_/ /_ |/ |/ / 
/_/    \___//_/ /_//____/ \____//_/    /_/      /_/  \____/____/|__/
WARNING: You are running this container as root, which can cause new files in
mounted volumes to be created as the root user on your host machine.
To avoid this, run the container by specifying your user's userid:
$ docker run -u $(id -u):$(id -g) args...
root@964b9789b1b1:/tf# 
```
```console
root@964b9789b1b1:/tf# apt install git -y && git clone https://github.com/mswawola-cegep/csfoy-420-a59-sf.git
```
Recuperer le token juypter pour se logger le site.
```console
root@964b9789b1b1:/tf# jupyter notebook list
Currently running servers:
http://0.0.0.0:8888/?token=f70b696e4691d365a5c4ad67ee404bd9e6fe795d068e64c8 :: /tf
```

# Manual installation

## Install basic ubuntu tools

## Install Nvidia Tools

Follow the instructions available on (https://www.tensorflow.org/install/gpu?hl=fr#ubuntu_1804_cuda_101)

```console
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.1.243-1_amd64.deb
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo dpkg -i cuda-repo-ubuntu1804_10.1.243-1_amd64.deb
sudo apt-get update
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt install ./nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt-get update

# Install NVIDIA driver
sudo apt-get install --no-install-recommends nvidia-driver-450
# Reboot. Check that GPUs are visible using the command: nvidia-smi

# Install development and runtime libraries (~4GB)
sudo apt-get install --no-install-recommends \
    cuda-10-1 \
    libcudnn7=7.6.5.32-1+cuda10.1  \
    libcudnn7-dev=7.6.5.32-1+cuda10.1


# Install TensorRT. Requires that libcudnn7 is installed above.
sudo apt-get install -y --no-install-recommends libnvinfer6=6.0.1-1+cuda10.1 \
    libnvinfer-dev=6.0.1-1+cuda10.1 \
    libnvinfer-plugin6=6.0.1-1+cuda10.1
```
## Install a graphical interface

source: https://medium.com/google-cloud/graphical-user-interface-gui-for-google-compute-engine-instance-78fccda09e5c

source: https://www.tecmint.com/install-and-configure-vnc-server-on-ubuntu/

Install gnome for ubuntu.
```bash
 sudo apt install ubuntu-gnome-desktop
```

Install a VNC server
```
sudo apt install tigervnc-standalone-server tigervnc-common tigervnc-xorg-extension tigervnc-viewer
```

Start the vnc server to be prompted to create a password.
```
vncserver
```

Pour kill un ecran sur le server: (:1) (:2) ou (:N)
```bash
cummings_guillaume@rage-with-gpu:~$ vncserver -kill :1
Killing Xvnc4 process ID 15997
```

Faire un backup de la config du vncServer avant de jouer dessus:
```
mv ~/.vnc/xstartup ~/.vnc/xstartup.bak
```

Creer un nouveau fichier de config vnc with:
```
nano ~/.vnc/xstartup
```

Saisir ceci:
```
#!/bin/sh
exec /etc/vnc/xstartup
xrdb $HOME/.Xresources
vncconfig -iconic &
dbus-launch --exit-with-session gnome-session &
```

Modifier les permissions du fichier de config :
```
sudo chmod +x ~/.vnc/xstartup
```

Create a file with this:
```
sudo nano /etc/vnc/xstartup
```

add this inside :
```
#!/bin/bash
unset SESSION_MANAGER
exec /etc/X11/xinit/xinitrc
```

Give the read permission to everyone:
```
sudo chmod 0755 /etc/vnc/xstartup
```

Pour les ecrans disponible sur le serveur:
```bash
cummings_guillaume@rage-with-gpu:~$ vncserver -list
TigerVNC server sessions:
X DISPLAY #     PROCESS ID
:1              3623
```

To verify that the installation is working, you can check if the port is open.
```bash
cummings_guillaume@rage-with-gpu:~$ nc localhost 5901
RFB 003.008
```
*Add a firewall(link to section) rule to allow ingress on 5901.

Verifier la connexion a distance
```
nc 104.197.91.140 5901
```
Tunnel SSH, si la securite est un concern.
```
ssh -i ~/.ssh/gcp-key -L 5901:127.0.0.1:5901 -C -N -l guillaumecummings 35.230.15.215
```

If you cant connect after setting the firewall rule, try:
```
vncserver -localhost no
```

If after pip install jupyter notebook you can't start jupyter:
```
~/.local/bin/jupyter-notebook
```

Demarrer vnc server au startup ???

Regarder le script de demarage:
```
cummings_guillaume@rage-with-gpu:~$ grep Exec= /usr/share/xsessions/*.desktop
/usr/share/xsessions/gnome-classic.desktop:Exec=gnome-session-classic
/usr/share/xsessions/gnome-classic.desktop:TryExec=gnome-session
/usr/share/xsessions/gnome.desktop:Exec=gnome-session
/usr/share/xsessions/gnome.desktop:TryExec=gnome-session
/usr/share/xsessions/gnome-xorg.desktop:Exec=gnome-session
/usr/share/xsessions/gnome-xorg.desktop:TryExec=gnome-session
/usr/share/xsessions/ubuntu-communitheme-snap.desktop:Exec=env GNOME_SHELL_SESSION_MODE=ubuntu-communitheme /snap/communitheme/current/session
/usr/share/xsessions/ubuntu-communitheme-snap.desktop:TryExec=/snap/communitheme/current/session
/usr/share/xsessions/ubuntu.desktop:Exec=env GNOME_SHELL_SESSION_MODE=ubuntu gnome-session --session=ubuntu
/usr/share/xsessions/ubuntu.desktop:TryExec=gnome-shell
```

## Jupyter Notebook

download
```
pip3 install virtualenv
```

create env
```
cd ~ && virtualenv tensorflow-env
```

activate the env
```
source tensorflow-env/bin/activate
```










