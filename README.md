
# Instalacion de RKE2 + Rancher en alta disponibilidad

Se brinda una guia de desplegar el aplicativo de RKE2 y Rancher en alta disponibilidad con 3 nodos master

## Requerimientos

    - Acceso a sudo
    - Desactivar fiewalld (ufw)

## VPC:

    - 10.98.72.101
    - 10.98.72.102
    - 10.98.72.103

## Hostname

    - AMKUBDEVRKEN01A
    - AMKUBDEVRKEN01B
    - AMKUBDEVRKEN02A

# Instalacion

## Validar ping o conexion entre nodos
```
ping 10.98.72.101
ping 10.98.72.102
ping 10.98.72.103

```

## Levantar el nodos de vagrant

```
cd Vagrant/
vagrant up
```

## Conectarse a un nodo

```
vagrant ssh AMKUBDEVRKEN01A
vagrant ssh AMKUBDEVRKEN01B
vagrant ssh AMKUBDEVRKEN02A
```
---


# Nodo principal (AMKUBDEVRKEN01A)

Las siguientes indicaciones aplican para lo que es el primero nodo master

## Crear archivo de configuracion
```
mkdir -p /etc/rancher/rke2/

nano /etc/rancher/rke2/config.yaml 
```

## Archivo de configuracion

Agregar el contenido del archivo `Config/master.yaml`

---
## Instalacion de ERK2

```
    curl -sfL https://get.rke2.io | sh -
    systemctl enable rke2-server.service
    systemctl start rke2-server.service
```

## Obtener token

```
cat /var/lib/rancher/rke2/server/node-token
```


# Nodos secuendarios

Las siguientes indicaciones aplican para el resto de nodos master que deseemos agregar

## Crear archivo de configuracion
```
mkdir -p /etc/rancher/rke2/

nano /etc/rancher/rke2/config.yaml 
```

## Archivo de configuracion


Agregar el contenido del archivo `Config/nodes.yaml`

---
## Instalacion de ERK2 

```
    curl -sfL https://get.rke2.io | sh -
    systemctl enable rke2-server.service
    systemctl start rke2-server.service
```



# Validaciones


## Revisar estado de cluster
```
# Estado de nodos
/var/lib/rancher/rke2/bin/kubectl get nodes --kubeconfig /etc/rancher/rke2/rke2.yaml

# Estado de pods
/var/lib/rancher/rke2/bin/kubectl get pods --all-namespaces --kubeconfig /etc/rancher/rke2/rke2.yaml

# Desintalar RKE2
/usr/local/bin/rke2-uninstall.sh

# Hacer snapshot de la imagen vagrant
vagrant snapshot save nombre_del_snapshot
```

---
Referencias
- [RKE HA](https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/kubernetes-cluster-setup/rke2-for-rancher)
- [RKE HA](https://docs.rke2.io/install/quickstart)