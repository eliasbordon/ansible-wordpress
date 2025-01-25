# Desafio 6 Anisble Wordpress

Este repositorio tiene como comedito explicar como podemos utilizar los roles de ansible para poder configurar y desplegar en un EC2 wordpress y MYSQL

## Tabla de Contenidos
- [Preparar el entorno](#1-preparar-el-entorno)
- [Descargar proyecto](#2-descargar-el-proyecto)
- [Estructura del proyecto](#estructura-de-proyecto)
- [Pasos previos a la ejecucion del playbook](#3-pasos-previos-a-la-ejecucion-del-playbook)
- [Ejecutar playbook](#4-ejecutar-el-playbook)
- [Varificar funcionamiento](#5-verificar-el-funcionamiento)
### 1.Preparar el entorno
Se debe dejar el entorno en donde se ejecutara el comando de ansible para aplicar el cambio por SSH en el EC2 de AWS.

1 - Se recomienda tener actualizado el entorno, al no tenerlo se recomienda ejecutar:
```bash
sudo apt update
```
2 - Una vez actualizado, se debe realizar la instalacion de Ansible si no existe en la PC anfitrion.
```bash
sudo apt install ansible
```
3 - Se recomienda tener actualizado la version de ansible
```bash
pip install --upgrade ansible
```

---

### 2. Descargar el proyecto

1 - Una vez preparado el entorno local, se debera clonar el repositorio de github del proyecto en la pc local.
```bash
git clone https://github.com/eliasbordon/ansible-wordpress.git
```
2 - Una vez clonado, se debera acceder a la carpeta del desafio.
```bash
cd ansible-wordpress
```

---

### Estructura de proyecto
```bash
ansible-wordpress/
├── playbook.yml
├── vockey.pem
├── inventary.ini
├── README.md
│   
├── group_vars/
│   ├── all.yml
├── roles/
│   ├── mysql/
│   │   ├── handlers/
│   │   │   ├── main.yml
│   │   ├── tasks/
│   │   │   ├── main.yml
│   ├── php/
│   │   ├── handlers/
│   │   │   ├── main.yml
│   │   ├── tasks/
│   │   │   ├── main.yml
│   │   ├── templates/
│   │   │   ├── wp-config.php.j2
│   │   │   ├── wordpress.conf.j2
```

### 3. Pasos previos a la ejecucion del playbook.
Se deberan realizar unos pasos previos a la ejecucion del playbook para poder establecer la conexion por SSH con el EC2 de AWS Academy.

1 - Se debe reemplazar el contenido de vockey.pem con el valor de la conexion establecida con AWS Academy.
Se debe acceder a AWS Academy, ir al Sandbox, y darle start.
una vez inicializado en el boton **Settings** -> **Show** permitira visualizar datos de configuracion para establecer la conexion, ahi se debera copiar el contenido de **SSH Key**.
El mismo puede ser reemplazado en el archivo **vockey.pem** existente.
O eliminar el existente y crear uno nuevo con el mismo nombre y la key recientemente copiada.
Se debera modificar los permisos del archivo (independientemente de cual opcion hayas realizado)
```bash
chmod 400 vockey.pem
```
En AWS se debe acceder a EC2 (Elastic Container Service) y crear una instancia de Sistema Operativo Ubuntu.
Pasos a seguir:
- Iniciar una instancia (en EC2)
- Colocar el nombre a gusto
- Sistema Operativo Ubuntu (esto es porque el codigo del playbook esta definido para Ubuntu)
- Seleccionar la version 22.04 ya que es mas estable que la version 24.04.
- Seleccionar el par de key vockey.pem existente.
- En la seccion de conectividad, habilitar la conexion por SSH, Http y https.
- Presionar Crear la Instancia.

Una vez que el campo **Comprobaciones de Estado** este en **2/2 comprobaciones superadas**, ir a **Acciones** -> **Seguridad** -> **Cambiar Rol de IAM**, y seleccionar el Role **LabInstanceProfile** y confirmar el cambio.

Una vez ya aplicado el cambio se debe seleccionar la instancia creada y copiar su IPv4 Publica la cual la agregaremos en el archivo **inventory.ini** en la seccion [demo] (en mi caso es 54.234.17.235).

---

### 4. Ejecutar el Playbook
Una vez realizado todos los pasos anteriores ya se puede ejecutar el comando de ansible para la instalacion del wordpress (wordpress, php, apache y mysql) en la instancia creada.
```bash
ansible-playbook -i inventory.ini playbook
```
El resultado en consola seria el siguiente:

![alt text](./image/playbook.png)

### 5. Verificar el funcionamiento
Una vez ejecutado el playbook y finalizado el mismo, para comprobar que haya realizado la instalacion correcta de wordpress y mysql, se debe acceder a la IP publica del EC2.
Se le tendra que sacar la **S** de HTTPS ya que el contenido se visualiza por HTTP (puerto 80).
Si no hay errores en la instalacion y configuracion del mismo, se deberia visualizar la interfaz de instalacion de wordpress.

![alt text](./image/Wordpress-Instalacion.PNG)


Si se quiere validar la creacion de la base de datos para wordpress, se puede acceder por SSH al EC2 y desde ahi acceder al mysql instalado.
Para acceder por SSH se debe ir al boton Conectar en EC2 (manteniendo seleccionado la instancia en cuestion) y te dara un comando como este:
```bash
ssh -i "vockey.pem" ubuntu@ec2-54-234-17-235.compute-1.amazonaws.com
```
Lo podras ejecutar en la PC que estas usando para ejecutar al playbook de ansible y una vez ingresado, si usas el siguiente comando podras acceder a mysql ingresando la credencial que se encuentra en el archivo all.yml en group_vars. 
```bash
sudo mysql -u root -p
```
Y una vez ingresado, se se ejecuta el siguiente comando se podra visualizar las bases de datos existentes en donde deberia aparecer la definida en el mismo all.yml.
```bash
SHOW DATABASES;
```

![alt text](./image/MySQL.PNG)

