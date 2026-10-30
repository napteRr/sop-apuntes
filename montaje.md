# Montaje de dispositivos y sistemas de archivos

## Concepto

Cada dispositivo tiene su propio sistema de archivos, que determina la
estructura de sus archivos. Al **montar** un dispositivo, sus archivos
pasan a integrarse en la estructura de directorios de Linux.

> **Montaje**: acción de incorporar los archivos almacenados en un
> dispositivo (o un sistema de archivos) a la estructura de directorios
> de Linux, generando un acceso hacia ellos.

Después del montaje se puede acceder a esos archivos desde un directorio
Linux y ejecutar sobre ellos cualquier comando Linux.

## Puntos de montaje

- Son **directorios** creados previamente desde los cuales se accede a
  los archivos del dispositivo.
- Al montar, los archivos adoptan el sistema de archivos de nodos-i
  (típicamente `ext4`).
- `ext4` soporta archivos de hasta ~16 TB.

## `/etc/fstab`

Archivo de texto regular que contiene la información necesaria para
montar sistemas de archivos o dispositivos. Solo el superusuario puede
editarlo.

El montaje se realiza de forma **secuencial**. Para que sea automático
al arranque, toda la información debe estar en `/etc/fstab`; si no, el
montaje debe hacerse manualmente.

### Estructura (6 campos)

| # | Campo | Ejemplo | Descripción |
|---|---|---|---|
| 1 | Dispositivo (file system) | `/dev/hda1` | Disco/partición a conectar (`hda` = primer disco IDE maestro; `1` = primera partición) |
| 2 | Punto de montaje | `/home` | Carpeta donde aparecerán los archivos. **Sin espacios intermedios** |
| 3 | Tipo de sistema de archivos | `ext4` | Formato de la partición (`xfs`, `swap`, `vfat`, `ntfs`, etc.) |
| 4 | Opciones de montaje | `defaults` o `defaults,usrquota,grpquota` | Permisos y funciones. Varias opciones separadas por comas **sin espacios** |
| 5 | Dump (respaldo) | `0` | `1` = activa copia de seguridad con `dump`; `0` = desactivado (lo habitual) |
| 6 | Pass (chequeo `fsck`) | `2` | `0` = no revisar; `1` = prioridad máxima (solo partición raíz `/`); `2` = resto de particiones |

### Ejemplo con errores y su corrección

Línea con errores:

```
/dev/hda / home ext4 default,usrquota,grpquota 0 1
```

Errores:

- `/dev/hda` apunta al disco entero en vez de a una partición (`hda1`).
- `/ home` tiene un espacio en medio: `fstab` separa columnas por
  espacios y se confunde.
- `default` le falta la `s` final (debe ser `defaults`).
- El `1` final en el campo Pass es exclusivo de la partición raíz (`/`);
  para `/home` debe ser `2`.

Línea corregida (básica):

```
/dev/hda1   /home   ext4   defaults   0   2
```

Línea corregida (con cuotas de disco):

```
/dev/hda1   /home   ext4   defaults,usrquota,grpquota   0   2
```

### Montaje automático vs. manual

- **Automático**: si la información está en `/etc/fstab`, el sistema monta
  al arrancar.
- **Manual**: si no está en `fstab`, montar a mano con:
  - `mount /dev/[nombre_del_archivo_especial]`
  - `mount /media/[punto_de_montaje]`

## Remontar en caliente

Aplica al instante los cambios hechos en `/etc/fstab` (por ejemplo
activar `usrquota`/`grpquota`) sin reiniciar ni desconectar usuarios.

```bash
mount -o remount /home
```

- `mount`: monta discos/particiones.
- `-o remount`: vuelve a montar en caliente una unidad ya montada.
- `/home`: punto de montaje al que se aplican los cambios.

## Opciones de montaje (campo 4)

- `defaults`: opciones estándar (lectura/escritura, montaje automático,
  permite ejecutar binarios, etc.).
- `usrquota`: habilita límites de espacio por **usuario**.
- `grpquota`: habilita límites de espacio por **grupo**.
## Dispositivos de bloque (`lsblk`)

`lsblk` lista los dispositivos de bloque disponibles.

Ejemplo:

```
NAME         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0          7:0    0  72,8M  1 loop
loop1          7:1    0  72,8M  1 loop
mmcblk0      179:0    0 116,5G  0 disk
├─mmcblk0p1  179:1    0   512M  0 part /boot/efi
└─mmcblk0p2  179:2    0   116G  0 part /
mmcblk0boot0 179:8    0     4M  1 disk
mmcblk0boot1 179:16   0     4M  1 disk
```

Campos:

- `name`: nombre del archivo especial que representa al dispositivo
  (ej. `sda`, `mmcblk0`).
- `maj:min`: números que identifican al dispositivo para el kernel.
- `rm`: removible (`1` = sí, `0` = no).
- `size`: tamaño (MB si es menor a 1 GB, G si es mayor).
- `RO`: solo lectura (`1` = sí, `-` = no).
- `type`: tipo de dispositivo (`disk`, `part`, `loop`, etc.).
- `MOUNTPOINTS`: punto de montaje; vacío si el dispositivo no está
  montado.
