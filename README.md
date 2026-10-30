# SOP — Apuntes organizados (clase de Clarisa)

Apuntes reorganizados y depurados a partir de las notas de clase.
Se eliminaron repeticiones y se ordenaron los temas de forma lógica
(montaje → `fstab` → cuotas → comandos → ejercicios).

## Contenidos

- [Montaje de dispositivos y sistemas de archivos](./montaje.md)
- [Cuotas de disco](./cuotas.md)
- [Ejercicios resueltos y comandos de espacio](./ejercicios.md)
- [Administración de usuarios y grupos](./usuarios.md)

## Referencia rápida

| Tarea | Comando |
|---|---|
| Ver `fstab` | `cat /etc/fstab` |
| Remontar en caliente | `mount -o remount /home` |
| Crear/actualizar archivos de cuota | `quotacheck -cug /home` |
| Activar cuotas | `quotaon /home` |
| Desactivar cuotas | `quotaoff /home` |
| Editar cuota de usuario | `edquota -u usuario` |
| Editar cuota de grupo | `edquota -g grupo` |
| Editar período de gracia | `edquota -t -u usuario` |
| Ver cuotas de todos | `repquota -a` |
| Ver cuota de un usuario | `quota -u usuario` |
| Ver cuota de un grupo | `quota -g grupo` |
| Espacio por sistema de archivos | `df` |
| Espacio por archivo/directorio | `du` |
| Listar dispositivos de bloque | `lsblk` |
| Crear usuario | `useradd -u UID usuario` |
| Modificar usuario | `usermod -l nuevoNombre usuario` |
| Crear grupo | `groupadd -g GID grupo` |
| Cambiar propietario | `chown nuevoDueño archivos` |
| Eliminar usuario | `userdel usuario` |

## Convenciones

- `#` delante de un comando = se ejecuta como **superusuario (root)**.
- `$` delante de un comando = lo puede ejecutar **cualquier usuario**.
- Los bloques de disco se cuentan en unidades de **1 KB** (1 bloque = 1 KB).
- Un **inodo** representa un archivo creado.