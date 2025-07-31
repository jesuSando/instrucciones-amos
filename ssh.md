# Instalación y Configuración Inicial de AMOS (Debian 8) para Backend

Este manual prioriza la **habilitación de compiladores, SSH y acceso remoto**, de modo que podamos administrar el AMOS desde otro PC lo antes posible.  

Una vez habilitado SSH, podremos clonar el backend y configurar PM2 sin depender de pendrives.

---

## 1. Instalación de Debian 8

1. Inserta el pendrive booteable con Debian 8 y enciende el AMOS.
2. Ingresa al BIOS (tecla **Supr/Del**) y configura el pendrive como primera opción de booteo.
3. Inicia instalación gráfica.
4. Durante la instalación:
   - Usuario **root**: `wit321`
   - Usuario común:  
     - Usuario: `wit`  
     - Contraseña: `wit321`

> una vez que el sistema inicie por primera vez, inicia sesión como `wit` y dejar el archivo `node-v10.24.1-linux-x64.tar.gz` en el escritorio.

---

## 2. Configurar repositorios antiguos de Debian 8 (desde AMOS)
1. Configura los repositorios:
   ```
   su
   ```
   ```
   wit321
   ```
   ```
   nano /etc/apt/sources.list
   ```
   Sustituye por:
   ```
   deb http://archive.debian.org/debian jessie main contrib non-free
   deb http://archive.debian.org/debian-security jessie/updates main contrib non-free
   ```
   luego:
   ```
   echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check-valid-until
   echo 'APT::Get::AllowUnauthenticated "true";' > /etc/apt/apt.conf.d/99insecure-repo
   echo 'Acquire::AllowInsecureRepositories "true";' >> /etc/apt/apt.conf.d/99insecure-repo
   ```
   ```
   rm -rf /var/lib/apt/lists/*
   ```
   ```
   apt-get clean
   apt-get update
   ```

2. Instala herramientas básicas y compiladores sin firma:
   ```
   apt-get install make python --allow-unauthenticated
   apt-get install gcc g++ --allow-unauthenticated
   ```

3. Verifica:
   ```
   make --version
   gcc --version
   g++ --version
   python --version
   ```
   
---

## 3. Instalar y habilitar SSH (desde AMOS)

```
su
```
```
wit321
```
```
apt-get update
apt-get install openssh-server --allow-unauthenticated
systemctl status ssh
systemctl start ssh
exit
```

---

## 4. Instalar Node.js 10.24.1 (desde SSH)

1. Copia el archivo `node-v10.24.1-linux-x64.tar.gz` al escritorio del usuario `wit`.
2. Extrae e instala en `/opt`:

   ```
   cd ~/Escritorio
   tar -xzf node-v10.24.1-linux-x64.tar.gz
   ```
   ```
   su
   ```
   ```
   wit321
   ```
   ```
   mv node-v10.24.1-linux-x64 /opt/
   exit
   ```
3. Agrega Node.js al PATH editando `~/.bashrc`:

   ```
   nano ~/.bashrc
   ```
   agregar al final
   ```
   export PATH=/opt/node-v10.24.1-linux-x64/bin:$PATH
   ```
   ```
   source ~/.bashrc
   ```
4. Verifica la instalación:
   ```
   node -v
   npm -v
   ```
   
---

## 5. Instalar Git y clonar el backend (desde SSH)

1. Instalar Git en Debian 8 (sin firmar):
   ```
   su
   ```
   ```
   wit321
   ```
   ```
   apt-get install git --allow-unauthenticated
   ```
   ```
   exit
   ```
2. Clonar el backend desde GitHub en el escritorio del usuario wit:
   ```
   cd ~/Escritorio
   git clone https://github.com/dorian-cesar/backend_amos.git
   cd backend_amos
   npm install
   ```
3. Habilitar puertos usb de manera permanente:
   ```
   su
   ```
   ```
   wit321
   ```
   ```
   nano /etc/udev/rules.d/99-acm-permisos.rules
   ```
   dentro pegar esto:
   ```
   KERNEL=="ttyACM*", MODE="0777"
   ```
   actualizar rules:
   ```
   udevadm control --reload-rules
   udevadm trigger
   ```
   verificar con:
   ```
   ls -l /dev/ttyACM*
   exit
   ```
   reiniciar pc
   
---

## 6. Ejecutar el backend con PM2 desde (SSH)

1. Instala PM2 compatible:
   ```
   npm install -g pm2@2.10.4
   pm2 -v
   ```
2. Inicia el servidor:
   ```
   cd ~/Escritorio/backend_amos/src
   pm2 start server.js --name servidor-1
   pm2 list
   ```
3. Guardar configuración para reinicios:
   ```
   pm2 save
   pm2 startup
   ```
   Copia el comando que entrega PM2 y ejecútalo.
