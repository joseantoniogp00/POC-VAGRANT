# Configuracion Inicial:

### Después de tener las maquinas creadas, realizé una configuración base a las maquinas a traves del Vagrantfile

#### 1º Definir una direccion IP fija.
Como anotacion en este apartado _"private_network"_ se refiere a modo NAT y _"public_network"_ a modo, adaptador puente o bridge.
    `ubu1.vm.network "public_network", ip: "192.168.18.201"`

#### 2º Definir hostname para:
    ubu1: ubu1.vm.hostname = "ubu1"
    ubu2: ubu2.vm.hostname = "ubu2"

#### 3º Crear para cada maquina el usuario josan con contraseña y añadirlo al grupo sudo para poder ejecutar sudo con este usuario.
	ubu1: ubu1.vm.provision "shell", inline: <<-SHELL
		useradd -m -s /bin/bash josan     -->(Crea el usuario josan)
		echo "josan:josan1234" | chpasswd   --> (Establece la contraseña del usuario)
		usermod -aG sudo josan           -->(Añade el usuario josan al grupo sudo)
        SHELL

	ubu2: ubu2.vm.provision "shell", inline: <<-SHELL
		useradd -m -s /bin/bash josan     -->(Crea el usuario josan)
		echo "josan:josan1234" | chpasswd   --> (Establece la contraseña del usuario)
		usermod -aG sudo josan           -->(Añade el usuario josan al grupo sudo)
        SHELL

### Estos serían las modificaciones del Vagrantfile.


**- version de wsl usada (1 o 2?)**
`wsl --status`  **_(en el cmd de windows, dentro del wsl NO)_**
En mi caso la version que uso de wsl es wsl2.


**- donde están los discos que usan ubu1 y ubu2**
Los discos que usan ubu1 y ubu2 estan en la ruta que se muestra abajo, ademas de los discos
ubu1 (mi_mv_ubu1.img) y ubu2 (mi_mv_ubu2.img), podemos ver los box que tenemos descargados para instalarlos 
en otras maquinas.
`/var/lib/libvirt/images`

**- donde está el proceso de qemu que ejecuta las máquinas del vagrant**
ejecutando `ps aux` podemos ver todos los procesos que hay en nuestra maquina, solo tenemos que buscar el de qemu

**- Ejecutar "sysbench cpu run" en el guest (wsl) y en ubu1 para comparar rendimiento.**
Para esto primero hay que instalar sysbench cpu run y despues ejecutarlo, una vez ejecutado podemos observar las estadisticas de nuestros recursos del equipo.

**WSL:**
Threads started!

CPU speed:
    events per second:  4978.72

General statistics:
    total time:                          10.0005s
    total number of events:              49793

Latency (ms):
         min:                                    0.19
         avg:                                    0.20
         max:                                    0.68
         95th percentile:                        0.21
         sum:                                 9990.09

Threads fairness:
    events (avg/stddev):           49793.0000/0.00
    execution time (avg/stddev):   9.9901/0.00

**ubu1:**
Threads started!

CPU speed:
    events per second:  4897.85

General statistics:
    total time:                          10.0007s
    total number of events:              48990

Latency (ms):
         min:                                    0.19
         avg:                                    0.20
         max:                                    1.14
         95th percentile:                        0.23
         sum:                                 9983.11

Threads fairness:
    events (avg/stddev):           48990.0000/0.00
    execution time (avg/stddev):   9.9831/0.00

**Cuantas veces hago una llamada a "vagrant configure"**
Cada vez que ejecutamos un comando "vagrant" hacemos que este lea la configuracion de vagrant en el Vagrantfile , por lo que entonces cada vez que ejecutemos un comando con vagrant estaremos haciendo una llamada a `vagrant configure` que este se encuentra en el Vagrantfile

**Y a "vmprovision"**
A "vmprovision" realizaremos una llamada cuando ejecutemos los comandos siguientes `vagrant up`(a este comando solo la primera vez),
`vagrant up --provision`o `vagrant reload --provision` estos comandos hacen una llamada al `config.vm.provision` que existe dentro del Vagrantfile.