# Administración de usuarios y grupos

## Archivos del sistema

- `/etc/passwd`: archivo regular con información de los usuarios.
- `/etc/group`: archivo con información de los grupos.

## Crear usuarios (`useradd` / `adduser`)

```bash
# useradd -u UID -g grupo_primario -G grupo_secundario -d home -s shell usuario
```

Opciones:

- `-u UID`: identificador de usuario.
- `-g grupo_primario`: grupo primario al que se incorpora (el sistema
  crea un grupo con el mismo nombre si no se indica).
- `-G grupo_secundario`: grupo secundario (opcional).
- `-d home`: directorio personal (por defecto `/home/usuario`).
- `-s shell`: shell por defecto (por defecto `bash`).

Ejemplo: crear el usuario `alumno10` con UID `810`:

```bash
# useradd -u 810 alumno10
```

El sistema crea automáticamente el directorio personal (`/home/alumno10`)
y el grupo `alumno10`.

## Modificar usuarios (`usermod`)

```bash
# usermod -u nuevoUID -l nuevoNombre -g nuevoGrupoPrimario usuarioActual
```

Ejemplo: renombrar `alumno10` a `alumno500`:

```bash
# usermod -l alumno500 alumno10
```

## Grupos

Crear grupo:

```bash
# groupadd -g GID nombre_del_grupo
```

Ejemplo: crear el grupo `sop30` con GID `950`:

```bash
# groupadd -g 950 sop30
```

Modificar grupo:

```bash
# groupmod -g nuevoNumero -n nuevoNombre nombreActual
```

Eliminar grupo:

```bash
# groupdel nombre_del_grupo
```

Ejemplo: crear el usuario `alumno80` con UID `870` e incorporarlo al
grupo `SOP30`:

```bash
# useradd -u 870 -g SOP30 alumno80
```

## Cambiar propietario (`chown`) y eliminar usuario

Antes de eliminar un usuario hay que reasignar sus archivos a un nuevo
dueño.

```bash
# chown nuevoDueño archivos
# find directorio -uid UIDactual -exec chown nuevoDueño {} \;
# userdel usuario
```

- `directorio`: a partir del cual se buscan los archivos.
- `-uid`: busca archivos de un determinado usuario.
- `{} \;`: indica que el comando se ejecuta sobre todos los archivos
  encontrados.

Ejemplo: reasignar archivos del usuario `alumno500` (UID 810) a
`alumno80` y luego eliminarlo:

```bash
# find ~ -uid 810 -exec chown alumno80 {} \;
# userdel alumno500
```

`~` indica el directorio de conexión (home).