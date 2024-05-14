# Diseño de Soluciones de Infraestructura

<p align="left" style="text-align:left;">
  <a href="https://www.duoc.cl/">
    <img alt="Github Universe" src="img/logo.png" width="1040"/>
  </a>
</p>

## Pre-requisitos

Ingresar al sitio de [play-with-docker](https://www.docker.com/play-with-docker/), para ello deben tener una cuenta en docker, si no la tienen, pueden crearla [aqui](https://hub.docker.com/signup)

## Implementación Docker

1.- Clonamos el repositorio git

```
git clone https://github.com/jveraduran/hello-world-container
```

2.- Ingresamos a la carpeta del proyecto

```
cd hello-world-container
```

3.- Compilamos la imagen mediante ```docker build```

```
docker build -t imagen-custom .
```

4.- Ejecutamos de manera local el contenedor con ```docker run```

```
docker run -p 80:80 -t imagen-custom
```

## Implementación Docker Swarm

Para ejecutar estas instrucciones, debemos ejecutar previamente los pasos 1 y 2 de **Implementación Docker**

1.- Iniciamos Docker Swarm

```
docker swarm init --advertise-addr 127.0.0.1
```

2.- Deployamos el Stack de PHP

```
docker stack deploy -c stack.yml image-custom
```

## Implementación Kubernetes

Ingresar al sitio de [play-with-k8s](https://labs.play-with-k8s.com), para ello deben tener una cuenta en GitHub, si no la tienen, pueden crearla [aqui](https://github.com/join)



1.- Iniciamos nodo maestro cluster

```
kubeadm init --apiserver-advertise-address $(hostname -i) --pod-network-cidr 10.5.0.0/16
```

2.- Iniciamos red en el cluster

```
kubectl apply -f https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kubeadm-kuberouter.yaml
```

3.- Parchamos temporalmente el Cluster, para permitir pods en el ControlPlane (Opcional, solo si no se configuran nodos)

```
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

4.- Creamos un despliegue de prueba

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/nginx-app.yaml
```

## Creación de imagen privada

### Autenticándonos en GitHub Container registry

1. Configura tu [access token](https://docs.github.com/en/packages/learn-github-packages/about-permissions-for-github-packages#about-scopes-and-permissions-for-package-registries), para habilitar funciones de GitHub como OAuth Access token y autenticación para acceder a GitHub API. 

    + Selecciona ```read:packages``` para descargar imágenes de contenedores y leer sus metadatos.
    
    + Selecciona ```write:packages``` para descargar y cargar imágenes de contenedores y para leer y escribir sus metadatos
    
    + Selecciona ```delete:packages``` para borrar imágenes de contenedores.

2. Graba tu PAT. Te recomiendio almacenar tu PAT acomo una variable de entorno
   
    ```
    export CR_PAT=YOUR_TOKEN
    ```

3. Usando CLI para tu tipo de contenedor, realizaremos un login en el servico de GitHub Container registry ```ghcr.io```.

    ```
    $ echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin
    > Login Succeeded
    ```

### Publicando en GitHub Container Registry

Después de iniciar sesión, ahora puedrás etiquetar y enviar tu imagen de Docker hacia GitHub Container Registry

1. Compila la imagen de este repositorio (o la que quieras)
   
    ```
    docker build -t IMAGE_NAME .
    ```

2. Etiqueta la imagen compilada
   
    ```
    docker tag IMAGE_ID ghcr.io/OWNER/IMAGE_NAME:TAG
    ```

3. Publica la imagen compilada
   
    ```
    docker push ghcr.io/OWNER/IMAGE_NAME:TAG
    ```
