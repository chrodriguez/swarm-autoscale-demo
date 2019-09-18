# Autoscaling con Docker Swarm

Para poder armar el laboratorio, compuesto por dos nodos, usamos Vagrant con
ansible.
Los requisitos entonces son:

* [VirtualBox](https://www.virtualbox.org/)
* [Vagrant](https://www.vagrantup.com)
* Ansible

La forma más simple de instalar Ansible, es correr:

```
apt install -y python3 y python3-venv
python3 -m venv ansible-env
source ansible-env/bin/activate
pip3 install ansible
```

Una vez con las dependencias se puede proceder a iniciar el laboratorio

## Iniciando el cluster

En el directorio de este repositorio, con el virtual env de python que nos
garantiza disponer de ansible, correr:

```
ansible-galaxy install -p ansible/roles/ atosatto.docker-swarm
```

Luego, podremos iniciar las VMs con Vagrant:

```
vagrant up
```


## TODO:

analizar bien:
*  https://github.com/askulkarni2/swarm-autoscaling-demo
* https://github.com/gianarb/orbiter
