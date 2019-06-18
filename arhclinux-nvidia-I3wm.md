#########################################
######[INICIAR SSH PARA INSTALAR.]#######
passwd
systemctl start sshd
ip a
#########################################

pacstrap /mnt base base-devel grub ntfs-3g networkmanager gvfs efibootmgr intel-ucode htop openssh

genfstab -pU /mnt >> /mnt/etc/fstab

arch-chroot /mnt

echo heros > /etc/hostname

ln -sf /usr/share/zoneinfo/America/Panama /etc/localtime

echo es_PA.UTF-8 UTF-8 >/etc/locale.gen
echo LANG=es_PA.UTF-8 >/etc/locale.conf
echo LANG=es_PA.UTF-8 >>/etc/environment
echo LC_TIME=es_PA.UTF-8 >>/etc/environment

vi ~/.bashrc
export LANGUAGE=es_PA.UTF-8
export LANG=es_PA.UTF-8
export LC_ALL=es_PA.UTF-8

locale-gen

hwclock -w

echo KEYMAP=us > /etc/vconsole.conf

grub-install --efi-directory=/boot/efi --bootloader-id='Arch Linux' --target=x86_64-efi

grub-mkconfig -o /boot/grub/grub.cfg

nano /etc/sudoers
%wheel ALL=(ALL) ALL

passwd

useradd -m -g users -G audio,lp,optical,storage,video,wheel,games,power,scanner -s /bin/bash <$USER>

passwd <$USER>

exit

umount -R /mnt

reboot

systemctl enable sshd.service
systemctl start sshd.service
systemctl enable NetworkManager
systemctl start NetworkManager
systemctl start systemd-resolved.service


sudo pacman -S git
cd /tmp
gpg --recv-key 465022E743D71E39
git clone https://aur.archlinux.org/aurman.git
cd aurman
makepkg -si


http://repo-ck.com
ENABLE MULTILIB /etc/pacman.conf
sudo pacman -S linux-ck-haswell linux-ck-haswell-headers
sudo mkinitcpio -p linux-ck-haswell 
sudo grub-mkconfig -o /boot/grub/grub.cfg
reboot

sudo pacman -Rcns linux

aurman -S aic94xx-firmware
aurman -S wd719x-firmware
sudo mkinitcpio -p linux-ck-haswell 


aurman -S laptop-mode-tools
sudo systemctl enable laptop-mode.service

sudo pacman -S fuse nvidia nvidia-utils nvidia-settings cuda nvidia-dkms lib32-nvidia-utils xorg-server xorg-xinit 


sudo mkdir /etc/pacman.d/hooks/

sudo vi /etc/pacman.d/hooks/nvidia.hook

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

sudo vi /etc/modprobe.d/nvidia.conf
blacklist nouveau


sudo vi /etc/mkinitcpio.conf
MODULES=(fuse ext4 nvidia nvidia_modeset nvidia_uvm nvidia_drm)

sudo vi /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="nvidia-drm.modeset=1 quiet"

sudo systemctl set-default graphical.target

sudo pacman -S pulseaudio pulseaudio-alsa alsa-utils alsa-plugins alsa-lib pavucontrol

sudo mkinitcpio -p linux-ck-haswell 
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo reboot

sudo nvidia-xconfig

sudo pacman -S ttf-liberation ttf-bitstream-vera ttf-dejavu ttf-droid ttf-freefont ttf-font-awesome ttf-ubuntu-font-family hunspell-es_pa

sudo pacman -S i3-wm i3lock i3status dunst lxdm-gtk3 lxappearance rxvt-unicode dmenu compton feh
sudo vi /etc/lxdm/lxdm.conf
	session=/usr/bin/i3
sudo systemctl enable lxdm.service

##Administrador de Archivos Ranger
sudo pacman -S ranger  atool elinks ffmpegthumbnailer highlight libcaca mediainfo odt2txt perl-image-exiftool poppler python-chardet

###Reproductor de musica y video
sudo pacman -S mplayer

sudo reboot

sudp pacman -S firefox firefox-i18n-es-mx

pacman -Rsdn $(pacman -Qqdt)
pacman -Scc
