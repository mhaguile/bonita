# Bonita+Postgresql en GKE

Version: 1.0beta


Esta configuraciones utilizan las imágenes de contenedores públicas:
* https://hub.docker.com/_/postgres/
* https://hub.docker.com/_/bonita/

## Notas de ambientación

Para la traducción de la configuración desde docker-compose a kubernetes utilizamos la herramienta [kompose](https://github.com/kubernetes/kompose)

Para pruebas locales, instalamos un nodo con [Minikube](https://kubernetes.io/docs/getting-started-guides/minikube/) 

## Software base:
* Bonita@7.6.3
* Postgresql@9.3

# Instalación

Clonar este repositorio:

```bash
$ git clone https://github.com/mhaguile/test.git
$ cd test
```

Crear el cluster en GKE

```bash
$ gcloud beta container clusters create bonita
```

Crear un disco permanente para la DB:

```bash
$ gcloud compute disks create --size 200GB bonitadb-disk
```

Ingresar al subdirectorio `gke`

Desplegar DB:

```bash
$ kubectl create -f db-deployment.yaml
```

Crear servicio para DB:
```bash
$ kubectl create -f db-service.yaml
```

Desplegar instancia de Bonita:
```bash
$ kubectl create -f bonita-deployment.yaml
```

Iniciar servicio de Bonita:
```bash
$ kubectl create -f bonita-service.yaml
```

Verificar que el puerto tcp/8080 de la instancia de bonita se encuentre publicado a Internet a través del balanceador:
```bash
$ kubectl describe service bonita                                                                                                  
Name:                     bonita
Namespace:                default
Labels:                   io.kompose.service=bonita
Annotations:              kompose.cmd=kompose convert
                          kompose.version=1.7.0 (767ab4b)
Selector:                 io.kompose.service=bonita
Type:                     LoadBalancer
IP:                       10.23.247.178
LoadBalancer Ingress:     35.196.152.17
Port:                     8080  8080/TCP
TargetPort:               8080/TCP
NodePort:                 8080  30863/TCP
Endpoints:                10.20.1.7:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason                Age   From                Message
  ----    ------                ----  ----                -------
  Normal  EnsuringLoadBalancer  18m   service-controller  Ensuring load balancer
  Normal  EnsuredLoadBalancer   17m   service-controller  Ensured load balancer
```


Abrir un navegador y apuntar a: `http://<IP LoadBalancer Ingress>:8080/bonita`


