
# Kind con ips expuestas al host

## Este archivo define un clúster Kind que permite exponer los nodos  IPs accesibles desde la misma red del host.
### Incluye múltiples opciones según el nivel de exposición que desees.



## ✅ Opción 1: **Red bridge personalizada** (recomendado si solo necesitas enrutar desde el host)

1. **Crear una red bridge Docker personalizada**:

```bash
docker network create kind-network \
  --subnet=172.20.0.0/16 \
  --gateway=172.20.0.1 \
  --ip-range=172.20.240.0/20 \
  --driver=bridge
```

2. **Archivo de configuración Kind (`kind-config.yaml`)**:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  disableDefaultCNI: true
  podSubnet: "10.244.0.0/16"
  serviceSubnet: "10.96.0.0/12"
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 6443
        hostPort: 6443
        listenAddress: "0.0.0.0"
  - role: worker
  - role: worker
```

3. **Crear el clúster Kind usando la red**:

```bash
kind create cluster --config kind-config.yaml --name my-cluster --image kindest/node:v1.29.0 --retain \
  --wait 5m --verbosity 3
```

4. **Conectar los contenedores Kind a la red**:

```bash
for node in $(docker ps --filter "name=my-cluster" --format "{{.Names}}"); do
  docker network connect kind-network $node
done
```

Ahora puedes ver las IPs:

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <nombre-contenedor>
```

---

## ⚠️ Opción 2: **Red Docker `macvlan` para obtener IPs en la misma red que el host**

> Necesitas una NIC física sin tráfico importante, ya que Docker en `macvlan` no puede comunicarse con el host por defecto.

1. Crea la red:

```bash
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 \
  kind-macvlan
```

2. Es más complejo hacer que Kind use `macvlan` directamente porque Docker no permite usar `macvlan` como red por defecto de contenedores. Tendrías que iniciar los nodos Kind manualmente y unirlos a esa red, lo cual rompe parte del flujo de Kind.

---

## ✅ Alternativa simple: usar `--network host` en Linux

Esto hace que el nodo del control plane use la red del host directamente (mismo namespace de red), **pero solo aplica a Linux**:

```yaml
nodes:
  - role: control-plane
    extraMounts:
      - hostPath: /etc/kubernetes
        containerPath: /etc/kubernetes
    extraPortMappings: []
    kubeadmConfigPatches:
      - |
        kind: InitConfiguration
        nodeRegistration:
          kubeletExtraArgs:
            node-ip: "IP_DEL_HOST"
```

Y lanzar así:

```bash
kind create cluster --config kind-config.yaml --name my-cluster --retain --wait 5m \
  --image kindest/node:v1.29.0 --verbosity 3 \
  --network host
```