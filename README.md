# 5.1 - Despliegue de Nginx en nodo único con K3s

### Estructura del proyecto

En esta imagen se muestra la estructura del proyecto en árbol. Se ha organizado el entorno de trabajo en carpetas: `logs` para los registros de instalación y comandos de verificación, `manifests` para los manifiestos Kubernetes, y `screenshots` para las capturas. Este orden facilita la trazabilidad del despliegue y permite documentar cada paso realizado.

![2025-05-28_12-44](https://github.com/user-attachments/assets/6d37e73e-1779-4172-beb6-ac34614a4226)

---

### Instalación de K3s

Aquí se observa el proceso de instalación de K3s ejecutado mediante el script oficial. El sistema descarga, verifica y configura automáticamente el binario `k3s`, además de generar el servicio correspondiente (`k3s.service`) y activarlo correctamente. Esto permite inicializar el clúster Kubernetes de forma rápida en un entorno de un solo nodo.

![2025-05-28_12-45](https://github.com/user-attachments/assets/712ff6fd-92d8-4477-8d24-6ee95e9f95b9)

---

### Verificación del clúster

Se muestran los comandos `kubectl get nodes` y `kubectl get pods -A` que validan que el clúster se encuentra en ejecución. El nodo principal está marcado como `Ready` y los pods del sistema (como CoreDNS, Traefik, metrics-server y otros) están funcionando correctamente en el namespace `kube-system`.

![2025-05-28_12-46](https://github.com/user-attachments/assets/d38e8249-16ca-476c-8873-448411426fb3)

---

### Despliegue de Nginx

Mediante `kubectl apply -f nginx-deployment.yaml` se despliega un pod de Nginx en el clúster. El manifiesto aplica un `Deployment` con dos réplicas y un `Service` expuesto como LoadBalancer. El clúster ha aceptado correctamente ambos recursos, generando los objetos de Kubernetes sin errores.

![2025-05-28_12-49](https://github.com/user-attachments/assets/66ae05d3-c3f7-474e-92ca-cfe6897ed9a8)

---

### Validación de recursos desplegados

La imagen muestra la verificación de que el despliegue de Nginx se ha aplicado correctamente. Se observan los dos pods corriendo (`Running`) y el `Service` creado, que expone el puerto 8080 a través de una IP externa (`172.16.181.130`) en el entorno. Esto confirma que el tráfico puede acceder al servicio desde fuera del clúster.

![2025-05-28_12-50](https://github.com/user-attachments/assets/a65e4dfd-4069-4fd9-b787-a6d47cfc2ddb)

---

### Acceso al servicio Nginx

Se accede al servicio Nginx desde un navegador mediante la IP externa y el puerto definido (8080). La aparición de la página por defecto de Nginx confirma que el despliegue fue exitoso y que la exposición del servicio a través de `LoadBalancer` funciona correctamente en el entorno de red local.

![2025-05-28_12-52](https://github.com/user-attachments/assets/14bdcf30-2ba7-4c67-bdd5-db88475acd6f)

---

### Validación con K9s

Desde la herramienta `K9s`, se validan los pods en tiempo real. Ambos pods desplegados por el `nginx-deployment` están en estado `Running`, con recursos correctamente asignados. También se verifica el nodo donde se ejecutan, que en este caso es `pedro-virtual-machine`, confirmando que todo el entorno funciona como se espera.

![2025-05-28_12-59](https://github.com/user-attachments/assets/e5149d19-c7b1-4a0c-9830-4136ae21d28c)

---


# 5.2 - Despliegue de clúster K3s en modo HA

A continuación se muestran las evidencias del despliegue exitoso de un clúster K3s en alta disponibilidad (HA), compuesto por un nodo master (control-plane) y dos agentes que se integran correctamente al clúster. Se validan los nodos, los pods desplegados y el funcionamiento del servicio.

### Estructura del proyecto

Se muestra la estructura del directorio de trabajo, que incluye los logs de instalación, los manifiestos YAML utilizados y las capturas de pantalla generadas a lo largo del proceso.

![2025-05-28_13-36](https://github.com/user-attachments/assets/7f8a7624-19af-49bc-99ce-f70347ea8977)

---

### Instalación del servidor maestro

Se ejecuta la instalación de `k3s` en modo `cluster-init`, desactivando Traefik, para preparar el nodo como master del clúster en alta disponibilidad.

![2025-05-28_13-54](https://github.com/user-attachments/assets/75248453-ebef-48b5-8a77-04bcba0a75af)

---

### Verificación del nodo maestro

Tras completar la instalación del servidor, se verifica que el nodo `k3s-serverx` esté en estado `Ready` y que el puerto 6443 (usado por el API server) esté en escucha.

![2025-05-28_13-55](https://github.com/user-attachments/assets/e73ced8b-c522-405d-ae9a-eb6b0ef5a1b3)

---

### Obtención del token del servidor

Se obtiene el token de unión necesario para agregar los agentes al clúster. Este token se utilizará como variable de entorno al instalar los agentes.

![2025-05-28_13-57](https://github.com/user-attachments/assets/162b2e79-17e5-4715-96eb-94a8bb6f277d)

---

### Validación del nodo activo

Se vuelve a comprobar el estado del nodo `k3s-serverx` antes de añadir los agentes, confirmando que sigue operativo y registrado como `master`.

![2025-05-28_14-11](https://github.com/user-attachments/assets/cfcb8305-497f-4d49-addf-d5c00468981f)

---

### Instalación de los agentes

Se instala K3s en cada uno de los agentes (agent1 y agent2), utilizando el token previamente obtenido y apuntando a la IP del servidor maestro. La instalación fue exitosa y no se requirió reiniciar el servicio en el agente donde ya estaba activo.

![2025-05-29_11-44](https://github.com/user-attachments/assets/00e4cc69-ace3-4890-a0a0-6a9d8f9aaf11)

---

### Verificación del clúster completo

Se comprueba que tanto el nodo master como los dos agentes están en estado `Ready` y correctamente identificados por el clúster, con sus respectivas IPs internas y sistemas operativos.

![2025-05-31_19-57](https://github.com/user-attachments/assets/aeb147a4-c9b1-4ba9-8638-44963ab654ca)

---

### Despliegue de nginx en el clúster

Se despliega una aplicación de ejemplo (nginx) mediante un manifiesto YAML que crea dos réplicas y un servicio NodePort para su exposición.

![2025-05-31_19-59](https://github.com/user-attachments/assets/28543151-fc42-4a90-bc8b-ecc5ca1de8eb)

---

### Validación de pods y servicios

Se comprueba que los dos pods de nginx estén corriendo, distribuidos en los agentes, y que el servicio `nginx-service` esté activo con el puerto NodePort asignado.

![2025-05-31_20-00](https://github.com/user-attachments/assets/56d47ed7-573c-40f9-8c79-cd7db7f8efaa)

---

### Visualización con K9s: Pods

Se accede a K9s para validar gráficamente que los pods del deployment están activos y correctamente repartidos entre `agent1` y `agent2`.

![2025-05-31_20-04](https://github.com/user-attachments/assets/b7ec2da2-1697-4976-a544-b556816fb779)

---

### Visualización con K9s: Logs

Finalmente, se revisan los logs de uno de los pods de nginx, donde se confirma que el contenedor se ha iniciado correctamente y está escuchando peticiones HTTP.

![2025-05-31_20-06](https://github.com/user-attachments/assets/726a7fce-4d44-44f5-8ef6-81d7e1e3d424)

---


# 5.3 - Despliegue en K3s desde docker-compose y validación con K9s

### Creación del archivo docker-compose.yml e instalación de Kompose

En esta primera imagen se muestra la creación del archivo `docker-compose.yml`, donde se definen los servicios `nginx` y `redis`. A continuación, se procede a instalar la herramienta `kompose`, necesaria para convertir archivos de Docker Compose en manifiestos compatibles con Kubernetes.

![Instalación de kompose y creación del docker-compose](screenshots/2025-05-31_20-23.png)

---

### Conversión de docker-compose a manifiestos de Kubernetes

Una vez instalado `kompose`, se ejecuta el comando `kompose convert`, que genera automáticamente los archivos YAML necesarios para desplegar los servicios definidos. En este caso se crean cuatro archivos: `nginx-deployment.yaml`, `nginx-service.yaml`, `redis-deployment.yaml` y `redis-service.yaml`.

![Conversión de docker-compose a manifiestos de Kubernetes con kompose](screenshots/2025-05-31_20-24.png)

---

### Aplicación de los manifiestos generados

Se realiza el despliegue de todos los manifiestos generados utilizando `kubectl apply -f .`. Aunque se genera un mensaje de advertencia por intentar aplicar nuevamente el archivo `docker-compose.yml`, los recursos de Kubernetes se crean correctamente.

![Aplicación de los manifiestos generados](screenshots/2025-05-31_20-25.png)

---

### Comprobación de recursos desplegados

La imagen muestra el resultado del comando `kubectl get all`, donde se puede verificar que tanto los pods como los servicios y los despliegues para `nginx` y `redis` están en estado `Running`. Se observa también que los recursos han sido replicados correctamente.

![Comprobación de recursos desplegados](screenshots/2025-05-31_20-26.png)

---

### Validación de servicios en K9s

Desde la interfaz de K9s se accede al apartado de servicios, donde se confirma que `nginx-service` ha sido expuesto mediante NodePort y `redis` a través de ClusterIP. Ambos servicios están activos y visibles en el entorno gráfico, validando así el correcto funcionamiento del despliegue.

![Validación de servicios en K9s](screenshots/2025-05-31_20-27.png)
