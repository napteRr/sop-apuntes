# Cuotas de disco en Linux

## Conceptos

- **Bloques**: unidades de 1 KB de espacio en disco.
- **Inodos**: representan archivos creados (cantidad de archivos).
- **Límite blando (soft)**: límite de advertencia. Al superarlo se activa
  un período de gracia.
- **Límite duro (hard)**: límite máximo. Al alcanzarlo el usuario/grupo
  debe liberar espacio para seguir usando el disco.
- **Período de gracia (grace)**: tiempo durante el cual se puede seguir
  usando el disco después de superar el límite blando. Por defecto:
  **7 días**.

### Límite blando vs. duro

- **Límite blando**: el usuario/grupo puede seguir usando espacio hasta
  que se cumpla el período de gracia **o** se llegue al límite duro (lo
  que ocurra primero).
- **Límite duro**: no se puede seguir usando el disco hasta liberar
  espacio.

## Configuración paso a paso

### 1. Habilitar cuotas en `/etc/fstab`

Agregar las opciones `usrquota` y/o `grpquota` en el campo de opciones:

```
/dev/hda1   /home   ext4   defaults,usrquota,grpquota   0   2
```

### 2. Remontar el sistema de archivos

```bash
mount -o remount /home
```

### 3. Crear/actualizar los archivos de cuota (`quotacheck`)

```bash
quotacheck -cug /home        # o: quotacheck -cugm /home
```

- Escanea la partición y calcula el espacio (bloques) y archivos (inodos)
  usados por cada usuario/grupo.
- Crea o actualiza `aquota.user` y `aquota.group` en la raíz del punto de
  montaje (ej. `/home/aquota.user`, `/home/aquota.group`).
- `-c` (create): crea los archivos desde cero.
- `-u` (user): cuotas de usuarios (`aquota.user`).
- `-g` (group): cuotas de grupos (`aquota.group`).
- `-m` (opcional): fuerza el escaneo aunque la partición esté montada en
  lectura/escritura.

> `quotacheck` toma una "foto" del estado actual; no vigila en tiempo
> real. La vigilancia continua la activa `quotaon`.

### 4. Activar el sistema de cuotas (`quotaon`)

```bash
quotaon /home            # o: quotaon -ugv /home
```

- Avisa al kernel que empiece a monitorear el disco en tiempo real.
- A partir de aquí se leen `aquota.user`/`aquota.group` y se registran
  bloques e inodos escritos; si se supera el límite, se bloquea.

Opciones comunes:

- `-u`: activa cuotas de usuario.
- `-g`: activa cuotas de grupo.
- `-v`: muestra mensaje de confirmación.
- `-a`: activa cuotas en todos los sistemas de `fstab` con opciones de
  cuota.

Comando inverso:

```bash
quotaoff /home           # desactiva la vigilancia de cuotas
```

### 5. Establecer/modificar cuotas (`edquota`)

```bash
edquota -u usuario       # cuotas de usuario
edquota -g grupo         # cuotas de grupo
```

- Solo el superusuario puede modificar las cuotas.
- Valida que el usuario/grupo exista leyendo `/etc/passwd` o `/etc/group`
  (no se le pasa la ruta, solo el nombre).
- Abre un editor (nano/vi) con una tabla temporal:

```
Filesystem  blocks  soft  hard  inodes  soft  hard
/dev/hda1   1024    5000  6000  12      50    60
```

Significado de los campos:

| Campo | Significado |
|---|---|
| `blocks` / `inodes` (primera columna) | Uso actual. **No modificar manualmente** |
| `soft` (bloques) | Límite blando de espacio |
| `hard` (bloques) | Límite duro de espacio |
| `soft` (inodos) | Límite blando de archivos |
| `hard` (inodos) | Límite duro de archivos |

Modificar período de gracia:

```bash
edquota -t -u usuario     # por usuario
edquota -t -g grupo       # por grupo
```

### 6. Verificación

```bash
repquota -a               # informe de uso de cuotas
quota -u usuario          # cuota de un usuario
quota -g grupo            # cuota de un grupo
```

Campos típicos de `repquota`:

| Campo | Significado |
|---|---|
| `user` | Nombre del usuario |
| `used` (blocks) | Bloques utilizados |
| `soft` | Bloques para llegar al límite blando |
| `hard` | Bloques para llegar al límite duro |
| `grace` | Período de gracia |
| `used` (files) | Archivos creados |
| `soft` | Archivos para llegar al límite blando |
| `hard` | Archivos para llegar al límite duro |

Campos típicos de `quota -u`:

| Campo | Significado |
|---|---|
| `blocks` | Bloques utilizados |
| `quota` | Bloques para llegar al límite blando |
| `limit` | Bloques para llegar al límite duro |
| `grace` | Período de gracia |
| `files (i-nodes)` | Archivos creados |
| `quota` | Archivos para llegar al límite blando |
| `limit` | Archivos para llegar al límite duro |

> Según la distribución, en lugar de `files` puede decir `i-nodes`.

## `#` vs `$`

- `#comando`: requiere **superusuario**.
- `$comando`: puede ejecutarlo **cualquier usuario**.

El superusuario puede modificar las cuotas; el usuario común solo puede
consultar las suyas.

## Pendientes / para investigar

- Cómo crear un archivo swap.
- Valores por defecto del período de gracia (7 días).