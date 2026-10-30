# Ejercicios resueltos y comandos de espacio

## Comandos de espacio

- `df`: espacio de disco utilizado por cada sistema de archivos.
- `du`: espacio de disco utilizado por cada archivo/directorio.

## Ejercicio 1

Datos del usuario:

```
file system
blocks        1236
quota (soft)  2100
limit (hard)  3800
grace
files (i-nodes)
quota
limit
grace
```

Pregunta: ¿puede crear un archivo de **1,14 MB**?

Conversión (1 bloque = 1 KB):

```
1,14 MB * 1024 KB/MB * 1 bloque/KB = 1167,36 bloques
```

Uso resultante:

```
1236 + 1167,36 = 2403,36 bloques
```

Comparación:

```
2403,36 < 3800  (límite duro)   -> no lo supera
2403,36 > 2100  (límite blando) -> lo supera
```

**Respuesta:** sí se puede crear, pero se supera el límite blando, por lo
que aparecerá una advertencia. Si se superara el límite duro, habría que
liberar espacio de disco.

## Ejercicio 2

Datos del usuario:

```
file system
blocks
quota
limit
grace
files (i-nodes)  15
quota            30
limit            42
grace
```

Pregunta: ¿puede crear **20 archivos**?

Cálculo de inodos:

```
15 + 20 = 35
```

Comparación:

```
35 > 30  (límite blando) -> lo supera
35 < 42  (límite duro)   -> no lo supera
```

**Respuesta:** sí se puede crear, pero con advertencia del sistema
operativo (se supera el límite blando, no el duro).