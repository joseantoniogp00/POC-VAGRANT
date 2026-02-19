#### 1º Instalacion de wsl en el equipo.

En un cmd ejecutamos el comando `wsl --install -d Ubuntu-22.04` _(-d indica que queremos instalar una distribución en concreto)_


#### 2º Abrimos el subsistema que hemos instalado (WSL) y preparamos e instalamos Vagrant.

Vamos al directorio `/home/nombre_usuario` y creamos una carpeta con el nombre del proyecto.

Después ejecutamos los siguientes comandos:
`wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg`

`echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list`

`sudo apt update && sudo apt install vagrant`

[Mas info sobre la instalacion de vagrant aqui](https://developer.hashicorp.com/vagrant/install)

#### 3º Despues de tenerlo instalado si queremos que vagrant se comunique con nuestro equipo fisico necesitamos guardar una variable de entorno en .bashrc 
_**(Abrimos ese archivo y en el final añadimos lo siguiente):**_

`export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"`

**NOTA**: _Si esta variable de entorno la ejecutas solamente, funcionara, pero una vez que cierres la sesion no quedara guardada y cuando vuelvas a iniciar no funcionara._
[Mas informacion sobre esta parte aquí](https://developer.hashicorp.com/vagrant/docs/other/wsl)

#### 4º Descargamos un box que utilizaran las maquinas con la distribución que necesitemos.
En mi caso es el siguiente:
`vagrant box add generic/ubuntu2204`

En este paso Vagrant instala por defecto un box llamado _Ubuntu/jammy64_, pero dependiendo que proveedor utilicemos este box puede dar problemas de compatibilidad.

#### 5º Instalación o comprobación de que tenemos kvm instalado.
- ejecutamos `ls /dev | grep kvm` esto sirve para comprobar que kvm esta instalado, si no nos muestra la palabra kvm, tendremos que instalarlo:

- `sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager`
Esto nos instalara kvm ademas de libvirt que es lo que hace de intermediario entre vagrant y kvm, libvirt lo que hace es permitirnos usar el comando `virsh` para interactuar con las maquinas que creemos.

- Despues tenemos que añadir el usuario al grupo libvirt y kvm para no tener que usar sudo cada vez:
`sudo usermod -aG libvirt nombre_usuario`
`sudo usermod -aG kvm nombre_usuario`

- Verificamos que _libvirt_ esta activo y corriendo **de no ser así no hay comunicacion entre vagrant y kvm**
`systemctl status libvirtd` (aqui debe de salir activo o de no ser así tenemos que activarlo):

- Activación de libvirt:
`sudo systemctl enable --now libvirtd`

- Ahora con esto preparado tenemos que instalar un plugin para que vagrant cree y gestione maquinas virtuales usando KVM como proveedor.
`vagrant plugin install vagrant-libvirt`

- Despues tenemos que instalar _build-essential_ que es un metapaquete que instala las herramientas basicas para compilar programas, **la herramienta que nos hace falta es make**.
`sudo apt install build-essential`

#### 6º Teniendo ya Vagrant y KVM preparados vamos a editar el fichero Vagrantfile.
Este debe encontrarse en `/home/nombre_usuario/carpeta_proyecto`, cuando abrimos el archivo nos vamos al final _(Los comentarios de por defecto que contiene el fichero los leemos y los que nos puedan ser utiles los dejamos y los demas lo borramos, así queda mucho mas limpio)_ y ahí creamos las maquinas que queramos, en mi caso 2:

Vagrant.configure("2") do |config|

  config.vm.define "ubu1" do |ubu1|
    ubu1.vm.box = "generic/ubuntu2204"
    ubu1.vm.provider :libvirt do |v|
      v.memory = 1024
      v.cpus = 1
    end
  end
  config.vm.define "ubu2" do |ubu2|
    ubu2.vm.box = "generic/ubuntu2204"
    ubu2.vm.provider :libvirt do |v|
      v.memory = 1024
      v.cpus = 1
    end
  end
end

#### 7º Por ultimo vamos a arrancar una de las maquinas y ver que funciona si todo lo hemos hecho bien:
Comando para arrancar una maquina en especifico:
`vagrant up nombre_maquina --provider=libvirt`

Comando para arrancar todas las maquinas al mismo tiempo:
`vagrant up --provider=libvirt`

Con el siguiente comando podemos ver en que estado estan las maquinas:
`virsh list --all`

Y con el comando: `vagrant ssh nombre_maquina`, nos conectamos a ella.



**Con todo este proceso hecho, nuestro entorno quedaría tal que asi:**
![diagrama](https://github.com/joseantoniogp00/POC-VAGRANT/blob/main/images/Diagrama.png?raw=true)