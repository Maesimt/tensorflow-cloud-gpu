# tensorflow-cloud-gpu

## Install a graphical interface

source: https://medium.com/google-cloud/graphical-user-interface-gui-for-google-compute-engine-instance-78fccda09e5c

Install gnome.
```bash
sudo apt-get install gnome-core -y
```

Install a VNC server
```
sudo apt-get install vnc4server -y
```

Start the vnc server to be prompted to create a password.
```
vncserver
```

To verify that the installation is working, you can check if the port is open.
```bash
cummings_guillaume@rage-with-gpu:~$ nc localhost 5901
RFB 003.008
```
*Add a firewall(link to section) rule to allow ingress on 5901.

After the verification, you have to kill the session.
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
unset SESSION_MANAGER
metacity &
gnome-settings-daemon &
gnome-panel &
```

Modifier les permissions du fichier de config :
```
sudo chmod +x ~/.vnc/xstartup
```

Verifier la connexion a distance
```
nc 104.197.91.140 5901
```
Tunnel SSH
```
ssh -i ~/.ssh/gcp-key -L 5901:127.0.0.1:5901 -C -N -l guillaumecummings 35.230.15.215
```

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
