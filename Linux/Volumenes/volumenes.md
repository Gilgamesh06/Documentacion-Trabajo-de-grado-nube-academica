# Volumenes

* En Linux, el concepto de **volúmenes** suele referirse a la organización y gestión del espacio de almacenamiento. A diferencias de Windows, donde ves letras de unidad (`c:`, `d:` ), Linux utiliza una estructura jerárquica unificada que comienza desde la raiz (`/`)
* Para entender bien los volúmenes, hay que distinguir entre el particionado tradicional y el  **LVM** (**Logical Volume Management**), que es el estándar moderno.

    ## Particiones Tradicionales vs Volúmenes Lógicos
    
    * Antiguamente, el disco se dividia en secciones fijas llamadas **particiones**. Si te quedabas sin espacio en una, era muy dificil ampliarla sin borrar datos.
    * Para solucionar esto, surgió **LVM**. Imagina que LVM es una capa de abstracción que convierte tus discos en una especie de "platilista" que puede modelar a tu gusto
	    
        ### Componentes de LVM
		
        * **PV (Physical Volume - Volumen Fisico):** Es el disco real o la párticion fisica (`/dev/sda`)
		* **VG (Volume Group - Grupo de Volúmenes):** Es una "bolsa" donde metes todos tus PVs Al combinarlos, creas un gran pozo de almacenamiento total
		* **LV (Logical Volume - Volumen Lógico):** Son las "particiones virtuales" que creas a partir del VG. Aquí es donde realmente instalas el sistema o gurdas tus archivos

    ## Comandos Listar y Ver Tamaño
	
    * `lsblk`
		
        * Es una herramienta que permite ver que discos y particiones tienes conectados al sistema
		* **Flags**
			
            * `-f` Muestra el tipo de sistema de archivos (ext4, vfat, xfs), y el **UUID**
			* `-a` Muestra todos los dispositivos, incluido los "loop devices"
			* `-p` Muestra la ruta completa del dispositivo (`/dev/sda1`)
			* `-o` Permite elegir que columnas ver `lsblk -o NAME, SIZE, TYPE`
	* `du`

    	* Es una herramienta que permite saber cuanto espacio ocupa tus archivos y carpetas en el sistema
		* **Flags**

			* `-s` Resume el total de la carpeta o archivo
			* `-h` Traduce los tamaños a un formato que entendemos los humanos (**KB**, **MB**, **GB**)
			
                ```bash
			    du -sh # se usan juntos
			    ```

    ## Comandos para ver LVM
	
    * **`pvscan` Physical Volume Scan**
	
    	* Este comando busca en todos los discos y particiones del sistema para identificar cuáles han sido inicializados como `PV`
	
    * **`vgscan` Volume Group Scan**

		* Este comando busca **Grupos de Volúmenes VG** en los dispositivos de bloque

	* **`lvscan` Logical Volume Scan**
		
        * Este comando busca todos los **Volúmenes Lógicos LV** en el sistema

    ## Comandos para montar o desmontar particion
	
    * **`mount` Montar**
		
        * Sirve para conectar un dispositivo (un disco duro, una partición LVM, un USB o una imagen ISO) a un directorio especifico llamado **punto de montaje**
		
            ```bash
		    sudo mount /dev/sdb2 /var/snap/microk8s/common
		    ```
	* **`umount` Desmontar**
		
        * Sirve para desconectar el dispositivo de forma segura. Es vital para asegurar que todos los datos pendientes se escriban en el disco y no se corrompan los archivos
			
            ```bash
			sudo umount /var/lib/libvirt
			```
    ## Comando para borrar partisionar disco
	
    * **`cfdisk`**

		* Es un editor de tablas de particiones con una **interfaz grafica basada en texto** (ncurses).
		* **Como se usa este comando:**
			
            * Para usarlo, normalmente necesitas permisos de superusuario y especificar el disco que quieres modificar:
			
            	```bash
				sudo cfdisk /dev/sda
			    ```
		* **Opciones comunes en el menú inferior**
			
            * Una vez dentro, veras un menú en la parte inferior que puedes navegar con las flechas izquierda/derecha:

            	* **New:** Crea una partición nueva en el espacio libre seleccionado
				* **Type:** Cambia el tipo de partición (Ej: de Linux norma a **LVM** o Swap)
				* **Delete:** Borrar la partición seleccionada
				* **Write:** Guardar los cambios en el disco
				* **Quit:** Sale del programa si guardar nada

    ## Comandos para montar un sistema de archivos a un partición
	
    * **`mkfs`**
		
        * Es un wrapper o envoltorio. Cuando lo ejecutas, el llama internamente a un programa especifico para cada sistema de archivos
		* **Sistemas de archivos**
			
            * **ext4:** Es el estandar por defecto en la mayoria de las distribuciones. Es robusto, rapido y estable
				
                ```bash
				sudo mkfs.ext4 /dev/sda1
				```
			* **xfs:** Ideal para servidores que mantenga  archivos muy grandes y grandes volúmenes de datos
				
                ```bash
			    sudo mkfs.xfs /dev/sda1
				```
			* **btrfs:** Un sistema moderno que permite snapshots, compresión y gestión de múltiples discos integrada
			
            	```bash
				sudo mkfs.btrfs /dev/sda1
				```
			* **vfat/fat32** El formato universal para memorias USB. Compatible con todo, pero no admite archivos de mas de 4GB
			
            	```bash
				sudo mkfs.vfat /dev/sda1
				```
			* **ntfs:** El sistema de archivos nativo de Windows
			
            	```bash
				sudo mkfs.ntfs /dev/sda1
				```
			    > requiere el paquete `nfts-3g`
			
            * **exfat:** La evolución de FAT32, ideal para memorias USB grandes o tarjetas SD, ya que permite archivos de mas de 4GB y es compatible con Windows y MAC
				
                ```bash
				sudo mkfs.exfat /dev/sda1
				```

    ## Comandos para cambiar tamanos en LVM
	
    * **Ampliar un Volumen Logico (Extend)**
		
        * Imagina que te has quedado sin espacio en tu volumen llamado `lv_datos` dentro del gurpo `vg_sistema`
		
        * **Paso 1: Añadir espacio al volumen**

			* Usamos el comando `lvextend`. Puedes añadir una cantidad especifica o usar todo el espacio libre restante
				
                * **Añadir 10GB** `
					
                    ```bash
					sudo lvextend -L +10G /dev/vg_sistema/lv_datos
					```
				* **Usar todo**
					
                    ```bash
					sudo lvextend -l +100%FREE /dev/vg_sistema/lv_datos
					```
		* **Paso 2: Agrandar el sistema de archivos**

			* Hasta ahora el "recipiente" es mas grande, pero el sistema de archivos (ext4 o xfs) no sabe que tiene mas espacio.
				
                * **Si es ext4**
					
                    ```bash
					sudo resize2fs /dev/vg_sistema/lv_datos
					```
				* **Si es XFS**
					
                    ```bash
				    sudo xfs_growfs /punto_de_montaje
					```
						- XFS usa el punto de montaje, no el dispositivo
	
    * **Reducir un Volumen Logico (Reduce)**
		
        > **Advertencia:** Este proceso es arriesgado. XFS no permite reducción, solo ext4. Siempre haz copia de seguridad
		
        * **Paso 1: Desmontar el volumen**
			
            * No se puede reducir un sistema de archivos mientras se esta usando
				
                ```bash
				sudo unmount /dev/vg_sistema/lv_datos
				```
		* **Paso 2: Comprobar errores**
			
            * Es obligatorio un chequeo de disco antes de redimensionar
				
                ```bash
				sudo e2fsck -f /dev/vg_sistema/lv_datos
			    ```
		
        * **Paso 3: Reducir el sistema de archivos**
			
            * Primero achicamos el contenido.
				
                ```bash
			    sudo resize2fs /dev/vg_sistema/lv_datos 5G
				```
				> **Ojo** se usa `resize2fs` se espera que sea sistema de archivos **ext4**
		
        * **Paso 4: Reducir el volumen lógico**
			
            * Ahora ajustamos el "recipiente" para que coincida
				
                ```bash
				sudo resize2fs /dev/vg_sistema/lv_datos 5G
				```

    ## Comandos para crear LVM
	
    * **Paso 1: Inicializar el Disco (Physical Volume - PV)**

		* Primero, le dice a Linux que un disco o partición especifica sera parte de LVM. Supongamos que tu disco nuevo es `/dev/sdb`
			
            ```bash
			sudo pvcreate /dev/sdb
			```
			* Escribe una cabecera en el disco para que que el sistema sepa que pertenece a LVM
			* **Verificar** `sudo pvs` o `sudo pvdisplay`

	* **Paso 2: Crear el Grupo de Volúmenes (Volume Group - VG)**
		
        * Ahora metes ese disco (o varios) en una "bolsa" o grupo virtual. Le daremos el nombre `vg_datos`
			
            ```bash
		    sudo vgcreate vg_datos /dev/sdb
			```
		* Crea un pozo común de almacenamiento. Si tuvieras otro disco (`/dev/sdc`) podrias poner ambos:
			
            ```bash
			sudo lvextend -l +100%FREE /dev/vg_sistema/lv_datos
			```
		* **Verificar:** `sudo vgs` o `vgdisplay`

	* **Paso 3: Crear el Volumen Logico (Logical Volume - LV)**
		
        * Ahora creas las "particiones virtuales"  dentro de ese grupo. Vamos a crear un volumen llamado `lv_proyectos` de 20 GB
			
            ```bash
			sudo lvcreate -L 20G -n lv_proyectos vg_datos
			```
			* `-L 20G` Define el tamaño,  es lo mismo que usar `--size`
			* `-n lv_proyectos` Define el nombre, es lo mismo que usar `--name`
			* `vg_datos` Es el grupo de donde sacara el espacio

		* Si quieres usar **Todo** el espacio disponible
			
            ```bash
			sudo lvcreate -l 100%FREE -n lv_proyectos vg_datos
			```
	    	* **Verificar:** `sudo lvs` o  `sudo lvdisplay`

	* **Paso 4 Dar formato y Montar**
		
        * LVM ya esta listo, pero el volumen esta vacio. Necesitas un sistema de archivos para empezar a guardar archivos
			
            * **Formatear (ext4)**
				
                ```bash
				sudo mkfs.ext4 /dev/vg_datos/lv_proyectos
				```
			* **Crear un directorio para montarlo**
				
                ```bash
				sudo mkdir /mnt/mis_datos
				```
			* **Montarlo**
				
                ```bash
				sudo mount /dev/vg_datos/lv_proyectos /mnt/mis_datos
				```

    ## Comandos de systemctl
	
    * **`status`: Ver el estado actual**
		
        * Antes de mover nada, lo primero es observar que esta pasando
			
            ```bash
			systemctl status nombre_del_servico 
			```
			* Te dice si el servicio esta **activo (running)**, **inactivo (dead)** o si fallo, Ademas, muestra las ultimas lineas del log
			* **Ejemplo**
				
                ```bash
				systemctl status libvirtd
				```
	* **`start`: Iniciar un servicio**
		
        * Su el servicio está detenido y quieres ponerlo a funcionar
			
            ```bash
			  sudo systemctl start nombre_del_servicio
		    ```
			* Arranca el proceso en el momento actual
			* **Ejemplo**
				
                ```bash
				sudo systemctl start libvirtd
				```
	* **`stop`: Detener un servicio**
		
        * Si necesitas apagar un proceso que esta corriendo
			
            ```bash
			sudo systemctl stop nombre_del_servicio
			```
			* Envía una señal al servicio para que se cierre de forma ordenada
			* **Ejemplo**
				
                ```bash
				sudo systemctl stop libvirtd
				```