Validar conexion entre nodos

```
ping < IP >
```

---

mkdir -p /etc/rancher/rke2/

nano /etc/rancher/rke2/config.yaml 


Ejecutar

```
    curl -sfL https://get.rke2.io | sh -
    systemctl enable rke2-server.service
    systemctl start rke2-server.service
```



---
Referencias
- [ERK HA](https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/kubernetes-cluster-setup/rke2-for-rancher)