

# Arranque

inicia gesto con ssh y establece la contraseña de root en 1234

```bash
gentoo dosshd passwd=1234
```

#### Opciones para el núcleo

- **gentoo**

  Núcleo por defecto con soporte para CPUs K8 (incluyendo las que tienen soporte para NUMA) y EM64T

- **gentoo-nofb**

  El mismo que *gentoo* pero sin soporte para framebuffer

- **memtest86**

  Prueba la RAM local en busca de errores

Junto con el núcleo, las opciones de arranque ayudan para ajustar el proceso de arranque aún más.

#### Opciones del hardware

- **acpi=on**

  Esto carga el soporte de ACPI y también hace que el demonio acpid se arranque desde el CD. Esto sólo es necesario si el sistema requiere de ACPI para funcionar correctamente. No es necesario para ofrecer soporte a Hyperthreading.

- **acpi=off**

  Desactiva completamente ACPI. Esto es útil en algunos sistemas antiguos y también es un requisito para el uso de APM. Esto deshabilitará el soporte para Hyperthreading de su procesador.

- **console=X**

  Esta opción configura acceso serie a la consola para el CD. La primera opción es el dispositivo, normalmente ttyS0 en x86, seguido de cualesquiera opciones de conexión que deben estar separadas por coma. Las opciones predeterminadas son: 9600,8,n,1.

- **dmraid=X**

  Esta opción permite pasar opciones al subsistema de mapeo de dispositivo RAID. Las opciones deben ir entre comillas.

- **doapm**

  Esta opción carga el controlador APM de apoyo. También requiere que `acpi=off`.

- **dopcmcia**

  Esta opción carga el soporte para PCMCIA y Cardbus hardware y también causa que el gestor de tarjetas pcmcia se arranque desde el CD en el inicio. Esto sólo es necesario cuando se arranque desde dispositivos PCMCIA/Cardbus.

- **doscsi**

  Esta opción carga el soporte para la mayoría de los controladores SCSI. Este es también un requisito para el arranque de la mayoría de los dispositivos USB, ya que utilizan el subsistema SCSI del núcleo.

- **sda=stroke**

  Esto permite al usuario particionar el disco duro entero, incluso cuando el BIOS no es capaz de gestionar discos grandes. Esta opción sólo se usa en máquinas con BIOS antiguos. Reemplace sda por el dispositivo que requiera esta opción.

- **ide=nodma**

  Esta opción fuerza la desactivación de la DMA en el núcleo. Varios chipsets IDE lo necesitan y también algunas unidades de CDROM. Se debe probar esta opción si el sistema tiene problemas para leer desde el CDROM IDE. También deshabilita que los ajustes predeterminados de hdparm se ejecuten.

- **noapic**

  Esta opción deshabilita el Controlador Avanzado de Interrupciones Programado (APIC) presente en las placas base modernas. Se sabe que puede causar algunos problemas en hardware antiguo.

- **nodetect**

  Esta opción desactiva todas las autodetecciones realizadas por el CD, incluyendo la detección de dispositivos y DHCP. Esto es útil para la depuración de fallos en el CD o en un controlador.

- **nodhcp**

  Esta opción deshabilita DHCP en las tarjetas de red que se han detectado. Esto es útil en redes que tienen únicamente direcciones estáticas.

- **nodmraid**

  Deshabilita el soporte para el mapeador de dispositivos RAID, tales como el que se utiliza en los controladores RAID IDE/SATA integrados en la placa base.

- **nofirewire**

  Esta opción deshabilita la carga de módulos Firewire. Esto sólo debería ser necesario si su hardware Firewire está causando un problema durante el arranque del CD.

- **nogpm**

  Esta opción deshabilita el soporte gpm para el ratón en la consola.

- **nohotplug**

  Esta opción deshabilita la carga de los guiones de inicio hotplug y coldplug en el arranque. Esto es útil para depuración de fallos en el CD o en un controlador.

- **nokeymap**

  Esta opción deshabilita la selección del mapa de teclado para seleccionar distribuciones de teclado que no sean la estadounidense (US).

- **nolapic**

  Esta opción deshabilita el APIC local en los núcleos para sistemas monoprocesador.

- **nosata**

  Esta opción deshabilita la carga de los módulos Serial ATA. Esto se utiliza cuando el sistema tiene problemas con el subsistema SATA.

- **nosmp**

  Esta opción deshabilita SMP, o el Multiprocesamiento Simétrico, en los núcleos con SMP habilitado. Esto es útil para la depuración de problemas relacionados con SMP que tienen algunos controladores y placas base.

- **nosound**

  Esta opción deshabilita el soporte para sonido y ajuste de volumen. Esto es útil para sistemas donde el sopore para sonido causa problemas.

- **nousb**

  Esta opción deshabilita la carga automática de los módulos USB. Esto es útil para la depuración de problemas con USB.

- **slowusb**

  Esta opción añade pausas extra en el proceso de arranque para CDROMs USB como en el IBM BladeCenter.

#### Gestión de volúmenes y dispositivos lógicos

- **dolvm**

  Habilita el soporte para la gestión de volúmenes lógicos (LVM) de Linux.

#### Otras opciones

- **debug**

  Habilita la depuración de código. Esto puede causar problemas, ya que muestra una gran cantidad de datos en la pantalla.

- **docache**

  Esta opción hace que se se almacene en caché (RAM) toda la porción de CD que contiene código que se va a ejecutar lo que permite al usuario desmontar /mnt/cdrom y montar otro CDROM. Esta opción necesita al menos el doble de capacidad en RAM que el tamaño del CD.

- **doload=X**

  Esta opción hace que el disco RAM de inicio cargue cualquier módulo de la lista, así como sus dependencias. Reemplace la X con el nombre del módulo. Se puede especificar varios módulos separándolos por comas.

- ***dosshd***

  Inicia sshd en el arranque, lo cual es útil para instalaciones desatendidas.

- ***passwd=foo***

  Establece lo que sigue al igual como la contraseña de root, lo cual es necesario para *dosshd* ya que la contraseña de root se ofusca por defecto.

- **noload=X**

  Esta opción hace que el disco RAM de inicio evite la carga de un módulo específico que puede estar causando un problema. La sintaxis es la misma que para doload.

- **nonfs**

  Deshabilita el inicio de portmap/nfsmount en el arranque.

- **nox**

  Esta opción hace LiveCD con X habilitado no inicie automáticamente X, sino que muestre la línea de órdenes.

- **scandelay**

  Esta opción hace que el CD pause durante diez segundos en algunas partes del proceso de arranque para permitir a los dispositivos lentos que se inicialicen y estén listos para su uso.

- **scandelay=X**

  Esta opción permite al usuario especificar un determinado retardo en segundos que se añade a algunas partes del proceso de arranque para permitir a los dispositivos lentos que se inicialicen y estén listos para su uso. Reemplace la X por el número de segundos a pausar.

# Particiones

#### Capas en el disco

| ordes | capas        |
| ----- | ------------ |
| 4     | Archivos     |
| 3     | Formato      |
| 2     | Particiones  |
| 1     | GPT/MBR      |
| 0     | Disco Físico |

### Crear Particiones

```bash
cfdisk /dev/sda
```

#### cfdisk example:

| Disposit. | Tamaño       | Tipo                         |
| --------- | ------------ | ---------------------------- |
| /dev/sda1 | 2M           | Sistema EFI                  |
| /dev/sda2 | 256M         | Sistema de ficheros de Linux |
| /dev/sda3 | 2G / 4G / 8G | Linux swap                   |
| /dev/sda4 | MAX          | Sistema de ficheros de Linux |

### Formato de particiones

```bash
mkfs.vfat /dev/sda1
mkfs.ext2 /dev/sda2
mkswap /dev/sda3
mkfs.btrfs /dev/sda4
```

### Montar particiones

### btrfs **/**

```bash
mount -o defaults,noatime,ssd,discard,space_cache,autodefrag /dev/sda4 /mnt/gentoo
```

### ext2 **/boot**

```bash
mkdir /boot
mount /dev/sda2 /mnt/gentoo/boot
```

### vfat **/boot/efi**

```bash
mkdir /mnt/boot/efi
mount /dev/sda1 /mnt/gentoo/boot/efi
```

### **swap**

```bash
swapop /dev/sda3
```

# Instalar sistema base

```bash
wget https://bouncer.gentoo.org/fetch/root/all/releases/amd64/autobuilds/20200412T214502Z/stage3-amd64-20200412T214502Z.tar.xz
```

# Configurar las opciones de compilación

```bash
nano -w /mnt/gentoo/etc/portage/make.conf
```



