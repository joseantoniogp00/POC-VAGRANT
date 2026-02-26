## Documento donde explique el flujo de trabajo con cli en vagrant

_Lo primero que debemos saber esque libvirt en el Vagrantfile actua como si fuese hipervisor KVM, así que cuando hablemos de libvirt estaremos hablando de KVM._

En este documento voy a exponer ciertos comandos que se usan comunmente en Vagrant y que es lo que hace cada comando y como interactua con el sistema para que funcione correctamente:

1º `vagrant status`: 
    ·Analiza el Vagrantfile que hay guardado ultimo.
    ·Consulta al proveedor libvirt el estado actual de la maquina o maquinas.
    ·Al recibir una respuesta de libvirt nos la muestra, "running", "shut off", "not created"...

2º `vagrant up`: Este comando puede funcionar de dos formas, la primera vez que creamos las maquinas, y a partir de la segunda vez que lo ejecutamos hace otra cosa distinta:

    1º Cuando la MV no esta creada y hacemos el primer arranque:
    ·Lee el Vagrantfile y busca primero la "box" que vamos a utilizar, en el caso de no tenerla preparda antes la buscaría y la descargaría 
    ·Importa la "box" al proveedor en mi caso libvirt (KVM)
    ·Aplica las configuraciones que hemos especificado en el Vagrantfile (CPU, RAM, configuracion de red...)
    ·Detecta la ip asiganda en el Vagrantfile o le asigna una autamaticamente, y realiza conexion ssh.

    2º Cuando la maquina esta creada pero apagada:
    Lo unico que hace es como si hiciera vagrant start, pero si no especificas la maquina, las arranca todas.

3º `vagrant halt`:
    ·Intenta primero un apagado limpio, se conecta por ssh, ejecuta el comando `sudo shutdown -h now` y espera que se apage completamente
    ·Si el apagado falla, directamente lo que hace es ordenarle al proveedor (libvirt) haga un apagado forzado.
    ·Actualiza la carpeta .Vagrant

4º `vagrant ssh`:
    ·Lee el Vagrantfile e identifica la VM a la que queremos conectarnos.
    ·Localiza la clave privada ssh
    ·Accede con el cliente via ssh del sistema.

5º `vagrant destroy`:
    ·Lee el Vagrantfile e identifica las VM que hay, y detecta la que queremos eliminar.
    ·Pide confirmacion de que queremos eliminarla.
    ·Verifica el estado de la VM, en el caso de que este encendida la apaga con `vagrant halt`
    ·Llama al proveedor para destruir la VM.