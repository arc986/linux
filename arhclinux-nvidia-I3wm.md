# 1. Instalación Remota.

## Configurar contraseña de root
```bash
passwd
```
## Inicia servicio ssh
```bash
systemctl start sshd
```
## Configura la conexión wifi
```bash
wifi-menu
```

## Muestra la IP
```bash
ip a
```

# 2. Instalar sistema base
```bash
pacstrap /mnt base base-devel grub ntfs-3g networkmanager gvfs efibootmgr intel-ucode htop openssh
```

## Tabla de particiones
```bash
genfstab -pU /mnt >> /mnt/etc/fstab
```

## Entramos del sistema virtual al real como root
```bash
arch-chroot /mnt
```

## Configurar el Nombre del equipo
```bash
#Nombre de la pc en minuscula sin acentos
export HOMBREPC=""
echo $HOMBREPC > /etc/hostname
```
## Configurar zona Horaria
```bash
ln -sf /usr/share/zoneinfo/America/Panama /etc/localtime
```

## Configurar idioma al español
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

## configurar hora
```bash
hwclock -w
```
## configurar teclado
```bash
echo KEYMAP=us > /etc/vconsole.conf
```
## instalar group efi
```bash
grub-install --efi-directory=/boot/efi --bootloader-id='Arch Linux' --target=x86_64-efi
```
## Reconfigura el grub
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```
## Configurar el grupo sudo
```bash
nano /etc/sudoers
#eliminamos el # frente a la liniea sigueinte
%wheel ALL=(ALL) ALL
```
## Configurar la contraseña de root
```bash
passwd
```
## Configurar un nuevo usuario
```bash
#Hombre del usuario sin acentos ni caracteres especiales
export USERR=""
useradd -m -g users -G audio,lp,optical,storage,video,wheel,games,power,scanner -s /bin/bash $USERR
passwd $USERR
```
## Salimos de del sistema real al virtual
```bash
exit
```

## Desmontamos las particiones y reiniciamos
```bash
umount -R /mnt
#recuerda remover el medio de instalación al reiniciar
reboot
```

# 3. Primer inicio
## iniciamos el servicio de SSH
```bash
systemctl enable sshd.service
systemctl start sshd.service
```
## Configuramos la red cableada
```bash
systemctl enable NetworkManager
systemctl start NetworkManager
systemctl start systemd-resolved.service
```

## Configura la conexión wifi
```bash
#el nombre que coloques a la red sera usado para iniciar el servicio
wifi-menu
export NOMBREDELAREDWIFI=""
netctl enable $NOMBREDELAREDWIFI
```
## Instalamos git y lo configuramos
```bash
sudo pacman -S git vim meld
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
git config --global core.editor vim
git config --global merge.tool meld
git config core.autocrlf true
```

## Instalamos aurman alternativa de yaourt
```bash
cd /tmp
gpg --recv-key 465022E743D71E39
git clone https://aur.archlinux.org/aurman.git
cd aurman
makepkg -si
```

## habilitamos el repositorio multilib
```bash
sudo vim /etc/pacman.conf
#eliminamos el # y debe quedar asi
[multilib]
Include = /etc/pacman.d/mirrorlist
```

## Configuramos los repositorios un kernel optimizado para nuestro procesador
```bash
#hay que segir la guia del sitio web
http://repo-ck.com
```
## Instalamos un kernel optimizado para nuestro procesador y reiniciamos
```bash
#el modelo depende de cada procesador
export MODELOCPU="haswell"
sudo pacman -S linux-ck-$MODELOCPU linux-ck-$MODELOCPU-headers
sudo mkinitcpio -p linux-ck-$MODELOCPU 
sudo grub-mkconfig -o /boot/grub/grub.cfg
reboot
```

## Eliminamos el kernel generico
```bash
sudo pacman -Rcns linux
```
## Se instalan driver faltantes
```bash
#En mi caso instalo driver faltantes que aparecen al ejecutar mkinitcpio -p linux-ck-haswell 
export MODELOCPU="haswell"
aurman -S aic94xx-firmware
aurman -S wd719x-firmware
sudo mkinitcpio -p linux-ck-$MODELOCPU 
```

## Se instalan herramientas adicionales
```bash
# en mi caso se isntala la siguiente herramienta ya que mi pc sin ella no es capas de apagarse
aurman -S laptop-mode-tools
sudo systemctl enable laptop-mode.service
sudo systemctl start laptop-mode.service
```
## Se instala los controladores de la tarjeta de video y dependencias
```bash
sudo pacman -S fuse nvidia nvidia-utils nvidia-settings cuda nvidia-dkms lib32-nvidia-utils xorg-server xorg-xinit vulkan-icd-loader lib32-vulkan-icd-loader
```

## Instalación del driver Nvidia
```bash
sudo mkdir /etc/pacman.d/hooks/

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
Exec=/usr/bin/mkinitcpio -P

#se añade a la lista negra el controlador nouveau para no ser usado nunca
sudo vi /etc/modprobe.d/nvidia.conf
blacklist nouveau

#se añade al kernel modulos que deben ser cargados automaticamente
sudo vi /etc/mkinitcpio.conf
MODULES=(fuse ext4 nvidia nvidia_modeset nvidia_uvm nvidia_drm)

#se modifica el group para añadir la configuracion de nvidia
sudo vi /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="nvidia-drm.modeset=1 quiet"

sudo systemctl set-default graphical.target
```

## Instalación del driver de sonido
```bash
sudo pacman -S pulseaudio pulseaudio-alsa alsa-utils alsa-plugins alsa-lib pavucontrol
```

## Reconfiguramos el kernel y reiniciamos
```bash
sudo mkinitcpio -p linux-ck-haswell 
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo reboot
```

## Reconfiguramos Xorg con los valores de Nvidia
```bash
sudo nvidia-xconfig
```


# 4. Instalacion del Interface Grafico

## Instalación de las fuentes Necesarias
```bash
sudo pacman -S ttf-liberation ttf-bitstream-vera ttf-dejavu ttf-droid ttf-freefont ttf-font-awesome ttf-ubuntu-font-family hunspell-es_pa
```
## Instalación de la interface grafica i3wm
```bash
sudo pacman -S i3-wm i3lock i3status dunst lxdm-gtk3 lxappearance rxvt-unicode dmenu compton feh
```
## Configuramos e iniciamos el servicio lxdm para que inicie i3wm por defecto
```bash
sudo vim /etc/lxdm/lxdm.conf
	session=/usr/bin/i3
sudo systemctl enable lxdm.service
```

## se instala el administrador de Archivos Ranger
```bash
sudo pacman -S ranger  atool elinks ffmpegthumbnailer highlight libcaca mediainfo odt2txt perl-image-exiftool poppler python-chardet
```

## Reproductor de musica y video
```bash
sudo pacman -S mplayer
```
## navegador web
```bash
sudp pacman -S firefox firefox-i18n-es-mx
```
## Iniciamos y configuramos nuestro firewall
```bash
sudo systemctl enable iptables
sudo systemctl start iptables
sudo systemctl status iptables
```
## Eliminar el beep de la terminal
```bash
sudo pacman -S xorg-xset
xset b off
```

# 5. Configuraciones Cosmeticas
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
vim ~/.Xresources
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


# 6. Fin 
## Reiniciamos
```bash
sudo reboot
```

## Busqueda de posibles problemas
```bash
journalctl -b | grep Fail
```

## limpiamos nustro sistema de paquetes huerfanos y del cache de las instalaciones
```bash
pacman -Rsdn $(pacman -Qqdt)
pacman -Scc
aurman -Scc
```
