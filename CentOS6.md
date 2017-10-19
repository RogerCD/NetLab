# Partición de la memoria principal
```
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
nvme3n1                   259:0    0  477G  0 disk
├─nvme3n1p1               259:1    0    1G  0 part /boot
└─nvme3n1p2               259:2    0  264G  0 part
  ├─vg_iti-lv_root (dm-0) 253:0    0  128G  0 lvm  /
  ├─vg_iti-lv_swap (dm-1) 253:1    0    8G  0 lvm  [SWAP]
  └─vg_iti-lv_home (dm-2) 253:2    0  128G  0 lvm  /home
```

BOOT 1024 MiB
LOGICAL_VOLUME 270340 MiB
VOLUME_GROUP 270336 MiB
HOME 131072 MiB
ROOT 131072 MiB
SWAP 8192 MiB


# Algunos comandos útiles

Obtener información del CPU.
```
lscpu
```

Listar espacio libre en discos.
```
df -h
```

Descomprimir un RPM.
```
rpm2cpio archivo.rpm | cpio -idv
```

cambiar permisos a directorios y archivos.
```
find ./ -type f -exec chmod 644 {} \;
find ./ -type d -exec chmod 755 {} \;
```


# Paquetes a Instalar con YUM
```
"Development Tools"
kernel-devel kernel-headers dkms
epel-release
kdebase-devel kdelibs-devel
mesa-libGL-devel mesa-libGLU-devel
ntfs-3g
lynx
tigervnc
tree
```


# Instalar controladores de NVIDIA

Verifica la existencia de la tarjeta gráfica.
```
lspci -nn | grep -i vga
```

debe aparecer algo así:
```
01:00.0 VGA compatible controller [0300]: NVIDIA Corporation GM107GL [Quadro K1200] [10de:13bc] (rev a2)
```

Instala dependencias de la aplicación.
```
yum -y groupinstalll "Development Tools"
yum -y install kernel-devel kernel-headers dkms
```

Desactiva los módulos correspondientes al controlador de noveau.
Añadie al archivo /etc/modprobe.d/blacklist.conf la siguiente línea:
```
blacklist nouveau
```

Reconstruye el Kernel.
```
mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r).img.bak
dracut -v /boot/initramfs-$(uname -r).img $(uname -r)
```

Por último, cambia al nivel de arranque 3 y corre el instalador.
```
init 3
./NVIDIA-Linux-x86_64-*.run
```

Ahora puedes correr un benchmark y disfrutar la tarjeta gráfica.
```
__GL_SYNC_TO_VBLANK=0 glxgears
```

# Instalación de Maya 2017
Es necesario tener esta dependencia instalada
```
yum -y install libXp
```

Posteriormente solo descomprime el paquete y corre el instalador.
Debes pedir ayuda a Uziel con las licencias.

## Configuración adicional

System > Preferences > Windows > Movement Key > Super (or "Windows logo")


# Instalación de VirtualBox
Tienes dos opciones.
1. Añadir el repositorio de VirtualBox e instalar con yum
2. Instalar el RPM

Mi recomendación es añadir al repositorio.
```
cp virtualbox.repo /etc/yum.repos.d/
yum update
yum -y install VirtualBox-5.1
```
## Extension Pack
```
VBoxManage list extpacks
wget -c http://download.virtualbox.org/virtualbox/5.1.28/Oracle_VM_VirtualBox_Extension_Pack-5.1.28-117968.vbox-extpack
VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-5.1.28-117968.vbox-extpack
```

Oneliner:
```
wget -qc http://download.virtualbox.org/virtualbox/5.1.28/Oracle_VM_VirtualBox_Extension_Pack-5.1.28-117968.vbox-extpack && VBoxManage extpack install --replace --accept-license=715c7246dc0f779ceab39446812362b2f9bf64a55ed5d3a905f053cfab36da9e Oracle_VM_VirtualBox_Extension_Pack-5.1.28-117968.vbox-extpack
```

Alternative oneliner:
```
wget -qc http://download.virtualbox.org/virtualbox/5.1.28/Oracle_VM_VirtualBox_Extension_Pack-5.1.28-117968.vbox-extpack && echo "y" | VBoxManage extpack install --replace Oracle_VM_VirtualBox_Extension_Pack-5.1.28-117968.vbox-extpack
```


## Grupo de usuarios vboxusers
Para utilizar correctamente dispositivos USB en la máquina virtual es necesario
añadirlos al gruupo de usuarios vboxusers.
```
usermod -a -G vboxusers alumno
```


# Auto login en GNOME
Con esto puedes iniciar sesión automáticamente a la interfaz gráfica de gnome.

Debes modificar el archivo /etc/gdm/custom.conf y añadir lo siguiente:
```
[daemon]
AutomaticLoginEnable = true
AutomaticLogin = alumno
```

# Instalación de JAVA
Descargar los RPM del JDK y JRE e instalar con
```
rpm -ivh jdk-*-linux-x64.rpm
rpm -ivh jre-*-linux-x64.rpm
```

Para seleccionar la versión de java a utilizar usa el comando
```
alternatives --config java
```

Configura las variables de entorno en el archivo /etc/enviroment
```
echo export JAVA_HOME=/usr/java/latest/jre
```

# Instalación de Arduino IDE
Descargar el paquete, descomprimir y copiar archivos.
```
wget -qc https://downloads.arduino.cc/arduino-1.8.3-linux64.tar.xz
mkdir /opt/arduino && cp -R arduino-*/ -t /opt/arduino/
```

Instalar dependencias extra.
```
yum -y install uisp avr-libc avr-gcc-c++ rxtx avrdude
```

Anadir usuario a grupos requeridos
```
usermod -a -G uucp,dialout,lock $USER
```


# Configurar archivos .desktop
Para utilizar algún ícono en particular colócalo en la carpeta /usr/share/icons

# SSH
```
cat ~/.ssh/id_rsa.pub | ssh USER@HOST "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```
