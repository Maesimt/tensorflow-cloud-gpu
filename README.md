# tensorflow-cloud-gpu

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
