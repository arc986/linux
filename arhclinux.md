# Instalación Remota por wifi.

### Configurar contraseña de root
```bash
passwd
```
### Inicia servicio ssh
```bash
systemctl start sshd
```
### Configura la red wifi
```bash
wifi-menu
```
### Muestra la IP
```bash
ip a
```


# Instalación Remota por cable.

### Configurar contraseña de root
```bash
passwd
```
### Inicia servicio ssh
```bash
systemctl start sshd
```
### Muestra la IP
```bash
ip a
```

# Particiones
## Crear Particiones
```bash
cfdisk /dev/sda
```
#### cfdisk example:
|Disposit.|Tamaño|Tipo|
|---|---|---|
|/dev/sda1|4M|Sistema EFI|                                    
|/dev/sda2|256M|Sistema de ficheros de Linux|
|/dev/sda3|4G|Linux swap|
|/dev/sda4|MAX|Sistema de ficheros de Linux|

#### Capas en el disco
|ordes|capas|
|---|---|
|4|archivos|
|3|formato|
|2|particiones|
|1|gpt/MRB|
|0|disco físico|

## Formatear particiones
```bash
mkfs.vfat /dev/sda1
mkfs.ext2 /dev/sda2
mkswap /dev/sda3
mkfs.btrfs /dev/sda4
```

# Montado de particiones
## Montar particion
### btrfs **/**
```bash
mount -o defaults,noatime,ssd,discard,space_cache,autodefrag /dev/sda4 /mnt
```
### ext2 **/boot**
```bash
mkdir /boot
mount -o /dev/sda2 /mnt/boot
```
### vfat **/boot/efi**
```bash
mkdir /mnt/boot/efi
mount -o /dev/sda1 /mnt/boot/efi
```
### **swap**
```bash
swapop /dev/sda3
```

# Instalar sistema base
## Instalacion Base
### Paquetes necesarios
```bash
pacstrap /mnt base base-devel grub ntfs-3g gvfs efibootmgr htop openssh linux-zen linux-zen-headers linux-firmware vim
```
### Paquetes opcionales
* networkmanager -> no se recomienda instalar si se instala netctl
* intel-ucode -> solo si usas procesadores intel
### Paquetes wifi y red cableada
* netctl
* wpa_supplicant
* dialog
* dhcpcd
### Generar la tabla de particiones
```bash
genfstab -pU /mnt >> /mnt/etc/fstab
```
### Entramos del sistema virtual al real como root
```bash
arch-chroot /mnt
```
### Configurar el Nombre del equipo
```bash
#Nombre de la pc en minuscula sin acentos dentro de las comillas "aqui"
export HOMBREPC=""
echo $HOMBREPC > /etc/hostname
#IPv4
echo "127.0.0.1 localhost $HOMBREPC" >> /etc/hosts
#IPv6
echo "::1 localhost $HOMBREPC" >> /etc/hosts
```
### Configurar zona Horaria
```bash
ln -sf /usr/share/zoneinfo/America/Panama /etc/localtime
```
### Configurar idioma al español
```bash
echo es_PA.UTF-8 UTF-8 >/etc/locale.gen
echo LANG=es_PA.UTF-8 >/etc/locale.conf
echo LANG=es_PA.UTF-8 >>/etc/environment
echo LC_TIME=es_PA.UTF-8 >>/etc/environment
echo export LANGUAGE=es_PA.UTF-8 >> ~/.bashrc
echo export LANG=es_PA.UTF-8 >> ~/.bashrc
echo export LC_ALL=es_PA.UTF-8 >> ~/.bashrc
locale-gen
```
### configurar hora
```bash
hwclock -w
```
### configurar teclado
```bash
echo KEYMAP=us > /etc/vconsole.conf
```
### instalar group efi
```bash
grub-install --efi-directory=/boot/efi --bootloader-id='Arch Linux' --target=x86_64-efi
```

## Grub
### Configurar grub
```bash
vim /etc/default/grub
#agregamos en GRUB_PRELOAD_MODULES los siguientes modulos
GRUB_PRELOAD_MODULES="part_gpt part_msdos btrfs"
```
### Reconfigura el Grub
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```
## Mkinitcpio
### Configurar mkinitcpio
```bash
vim /etc/mkinitcpio.conf
#Agregamos en MODULES el comando btrfs
MODULES=(btrfs)
#Agregamos en HOOKS al final el comando btrfs
HOOKS=(base udev autodetect modconf block filesystems keyboard fsck btrfs)
```
### Reconfigura el Mkinitcpio
```bash
mkinitcpio -p linux-zen
```

### Configurar el grupo sudo
```bash
visudo
#eliminamos el # frente a la liniea sigueinte
%wheel ALL=(ALL) ALL
```
### Configurar la contraseña de root
```bash
passwd
```
### Configurar un nuevo usuario
```bash
#Hombre del usuario sin acentos ni caracteres especiales, dentro de las comillas "aqui"
export USERR=""
useradd -m -g users -G audio,lp,optical,storage,video,wheel,games,power,scanner -s /bin/bash $USERR
passwd $USERR
```
### Salimos de del sistema real al virtual
```bash
exit
```
### Desmontamos las particiones y reiniciamos
```bash
umount -R /mnt
#recuerda remover el medio de instalación al reiniciar
reboot
```


# Primer inicio
### removemos paquetes inecesarios e instalamos otros
```bash
sudo pacman -Rncs vim
sudo pacman -S nvim
```

### iniciamos el servicio de SSH
```bash
systemctl enable sshd.service
systemctl start sshd.service
```

### Configuramos la red cableada NetworkManager
En caso de que instalaras networkmanager
```bash
systemctl enable systemd-resolved.service
systemctl start systemd-resolved.service
systemctl enable NetworkManager
systemctl start NetworkManager
```
### habilitamos el repositorio multilib
```bash
sudo vim /etc/pacman.conf

#Eliminamos el # del parametro NoExtract y aniadimos la siguente linea si instalamos i3 y debe quedar asi esto evita que instala cosas inecesarias
NoExtract   = usr/bin/cacafire usr/bin/aafire

#Eliminamos el # y debe quedar asi
[multilib]
Include = /etc/pacman.d/mirrorlist
```
### Reducir el número de terminales gertty a solo 2 reduce el consumo de ram
```bash
sudo nvim /etc/systemd/logind.conf
#se descomenta y se coloca el número 2
NAutoVTs=2
```

## Configura la conexión wifi
El nombre que coloques a la configuracion sera usado para iniciar el servicio
### Buscar redes wifi
```bash
wifi-menu
```
### Listas de redes configuradas
```bash
ls /etc/netctl/<NAME_RED_FILE>
```
### Activar iniciar conexion
```bash
netctl start <NAME_RED_FILE>
```
### Activar de forma permanente
```bash
netctl enable <NAME_RED_FILE>
```

## Instalación de git y configuracion
```bash
sudo pacman -S git nvim
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
git config --global core.editor nvim
```
### meld (opcional)
```bash
git config --global merge.tool meld
```

## Instalador aour
```bash

```

## Se instalan herramientas adicionales
```bash
# en mi caso se isntala la siguiente herramienta ya que mi pc sin ella no es capas de apagarse
aurman -S laptop-mode-tools
sudo systemctl enable laptop-mode.service
sudo systemctl start laptop-mode.service
```
## Nvidia
### Se instala los controladores de la tarjeta de video y dependencias
```bash
sudo pacman -S fuse nvidia nvidia-utils nvidia-settings cuda nvidia-dkms lib32-nvidia-utils xorg-server xorg-xinit vulkan-icd-loader lib32-vulkan-icd-loader
```

# Configuraciones del driver Nvidia adicionales
## Nvidia hooks
### Creamos el forlder para hooks de pacman
```bash
sudo mkdir /etc/pacman.d/hooks/
```
### Se crea el archivo nvidia.hook
```bash
sudo vim /etc/pacman.d/hooks/nvidia.hook
#Contenido del archivo nvidia.hook
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia

[Action]
Depends=mkinitcpio
When=PostTransaction
Exec=/usr/bin/mkinitcpio -P linux-zen
```
### Se configura el archivo modprobe de nvidia
```bash
#se añade a la lista negra el controlador nouveau para no ser usado nunca
sudo vi /etc/modprobe.d/nvidia.conf
blacklist nouveau
```
### Se configura el mkinitcpio para optimizar Nvidia
```bash
#se añade al kernel modulos que deben ser cargados automaticamente
sudo vi /etc/mkinitcpio.conf
MODULES=(fuse nvidia nvidia_modeset nvidia_uvm nvidia_drm)
```
### Se configura el grub para optimizar Nvidia
```bash
#se modifica el group para añadir la configuracion de nvidia
sudo vi /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="nvidia-drm.modeset=1 quiet"

sudo systemctl set-default graphical.target
```
### Reconfiguramos el mkinitcpio,grub y reiniciamos
```bash
sudo mkinitcpio -p linux-zen 
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo reboot
```
### Reconfiguramos Xorg con los valores de Nvidia
```bash
sudo nvidia-xconfig
```

## Instalación del driver de sonido
### Driver de sonido
```bash
sudo pacman -S pulseaudio pulseaudio-alsa alsa-utils alsa-plugins alsa-lib
```
### opcional
* pavucontrol


# Instalacion del Interface Grafico
### Instalación de las fuentes Necesarias
```bash
sudo pacman -S ttf-liberation ttf-bitstream-vera ttf-dejavu ttf-droid ttf-freefont ttf-font-awesome ttf-ubuntu-font-family hunspell-es_pa
```
### Instalar si se queire wallpapers
```bash
sudo pacman -S feh
```
### Eliminar el beep de la terminal
```bash
sudo pacman -S xorg-xset
#se pone dentro del archivo config del i3
xset b off
```

# Instalación de la interface grafica i3wm
```bash
sudo pacman -S i3-gaps i3lock i3blocks dunst lxappearance rxvt-unicode dmenu
```
## Gestor de login lxdm-gtk3
```bash
sudo pacman -S lxdm-gtk3
```
### Configuramos e iniciamos el servicio lxdm para que inicie i3wm por defecto
```bash
sudo vim /etc/lxdm/lxdm.conf
	session=/usr/bin/i3
sudo systemctl enable lxdm.service
```

# Administrador de Archivos
```bash
sudo pacman -S mc
```

# Reproductor de musica y video
```bash
sudo pacman -S mplayer
```

# Navegador web
```bash
sudp pacman -S firefox firefox-i18n-es-mx
```

# Seguridad
## Iniciamos y configuramos nuestro firewall
```bash
sudo systemctl enable iptables
sudo systemctl start iptables
sudo systemctl status iptables
```
## Instalamos y configuramos nuestro fail2ban
### Instalamos
```bash
sudo pacman -S fail2ban
```
### Configuramos nuestro archivo jail
```bash
sudo vim /etc/fail2ban/jail.local
#Contenido de archivo jail.local
[DEFAULT]
bantime = 1d

[sshd]
enabled   = true
filter    = sshd
banaction = iptables
backend   = systemd
maxretry  = 3
findtime  = 1d
bantime   = 1d
ignoreip  = 127.0.0.1/8

[Definition]
logtarget = /var/log/fail2ban/fail2ban.log
```
### iniciamos los servicos
```bash
sudo systemctl enable fail2ban.service
sudo systemctl start fail2ban.service
sudo systemctl status fail2ban.service
```

# Snap y AppArmor
## Instalar
```bash
yay -S snapd
sudo pacman -Ss apparmor
sudo systemctl enable --now apparmor.service
sudo systemctl enable --now snapd.apparmor.service
sudo systemctl enable snapd.socket
sudo reboot
```

# Configuraciones Cosmeticas
## VIM monokai theme
```bash
sudo pacman -S vim-molokai
vim ~/.vimrc
#contenido del archivo .vimrc
syntax on
colorscheme molokai
set t_Co=256
```

## Configuracion basica urxvt
```bash
nvim ~/.Xresources
#Configuraciones del archivo

URxvt.clipboard.autocopy: false

!! Appearance
urxvt.scrollBar: false
urxvt.background: black
urxvt.foreground: gray

!-- Xft settings -- !
Xft.dpi:        96
Xft.antialias:  true
Xft.rgba:       rgb
Xft.hinting:    true
Xft.hintstyle:  hintfull

! -- Fonts -- !
URxvt.font:xft:Monospace:pixelsize=12
URxvt.boldfont:xft:Monospace-Bold:pixelsize=12

!! Yeah, I am one of those, who use these keys in Vim :-(
urxvt.keysym.Home:          \033[1~
urxvt.keysym.End:           \033[4~
urxvt.keysym.Control-Up:    \033[1;5A
urxvt.keysym.Control-Down:  \033[1;5B
urxvt.keysym.Control-Left:  \033[1;5D
urxvt.keysym.Control-Right: \033[1;5C
```

# Fin 
## Reiniciamos
```bash
sudo reboot
```

## Busqueda de posibles problemas
```bash
journalctl -b | grep Fail
#or
journalctl --falied
```

## limpiamos nuestro sistema de paquetes huerfanos y del cache de las instalaciones
```bash
pacman -Rsdn $(pacman -Qqdt)
pacman -Scc
aurman -Scc
```


# KVM
### instalar kvm
```bash
sudo pacman -S virt-manager qemu vde2 ebtables dnsmasq bridge-utils openbsd-netcat dmidecode
```
### Agregar usuario al los grupos necesarios
```bash
sudo gpasswd -a $USER kvm
sudo gpasswd -a $USER polkitd
sudo gpasswd -a $USER libvirt
```

### iniciar servicio
```bash
sudo systemctl enable libvirtd.service
sudo systemctl start libvirtd.service
sudo systemctl status libvirtd.service
```