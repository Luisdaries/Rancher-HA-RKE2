
# Instalacion de RKE2 + Rancher en alta disponibilidad

Se brinda una guia de desplegar el aplicativo de RKE2 y Rancher en alta disponibilidad con 3 nodos master

## Requerimientos

    - Acceso a sudo
    - Desactivar fiewalld (ufw)
    - SO Linux
    - 2 vcpu
    - 4 Gb Ram
    - 40 Gb de espacio

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

```bash
ping 10.98.72.101
ping 10.98.72.102
ping 10.98.72.103

```

## Levantar el nodos de vagrant

```bash
cd Vagrant/
vagrant up
```

## Conectarse a un nodo

```bash
vagrant ssh AMKUBDEVRKEN01A
vagrant ssh AMKUBDEVRKEN01B
vagrant ssh AMKUBDEVRKEN02A
```
---


# Nodo principal (AMKUBDEVRKEN01A)

Las siguientes indicaciones aplican para lo que es el primero nodo master

## Crear archivo de configuracion

```bash
mkdir -p /etc/rancher/rke2/

nano /etc/rancher/rke2/config.yaml 
```

## Archivo de configuracion

Agregar el contenido del archivo `Config/master.yaml`

---
## Instalacion de ERK2

```bash
curl -sfL https://get.rke2.io | sh -
systemctl enable rke2-server.service
systemctl start rke2-server.service
```

## Obtener token

```bash
cat /var/lib/rancher/rke2/server/node-token
```


# Nodos secuendarios

Las siguientes indicaciones aplican para el resto de nodos master que deseemos agregar

## Crear archivo de configuracion

```bash
mkdir -p /etc/rancher/rke2/

nano /etc/rancher/rke2/config.yaml 
```

## Archivo de configuracion


Agregar el contenido del archivo `Config/nodes.yaml`

---
## Instalacion de ERK2 

```bash
curl -sfL https://get.rke2.io | sh -
systemctl enable rke2-server.service
systemctl start rke2-server.service
```

---

# Instalacion de rancher

## Instalar kubectl

```bash
snap install kubectl --classic
```
## configurar Kubectl

```bash
mkdir -p $HOME/.kube
sudo cp /etc/rancher/k3s/k3s.yaml $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```


## Instalar HELM

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

## Agregar libreria de Racher

```bash
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
helm repo update
```

## Crear namespace para rancher

```bash
kubectl create namespace cattle-system
```
## Instalar Cert - Manager

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.3/cert-manager.yaml
```

## Esperar a que los pods de Cert - Manager esten listo

```bash
kubectl get pods -n cert-manager -w
```

## Instalar Rancher - latest

```bash
helm install rancher rancher-stable/rancher   --namespace cattle-system   --set hostname=<dominio>   --set bootstrapPassword=admin
```

## Esperar a que los pods de Rancher esten listos

```bash
kubectl -n cattle-system rollout status deploy/rancher

```

## Revisar a que los servicios de k8s esten listos

```bash
kubectl get svc --all-namespaces -o wide
```
# Validaciones

```bash
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

