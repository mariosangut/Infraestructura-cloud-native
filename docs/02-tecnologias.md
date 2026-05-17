# 2. Tecnologías Utilizadas

En este proyecto se ha empleado un conjunto de herramientas tecnológicas que han permitido la automatización y el despliegue integral de la plataforma. A continuación se describen agrupadas por capa funcional.

---

## A. Capa de Infraestructura y Orquestación

### 2.1 Docker (mediante OrbStack)

Docker es una plataforma para el desarrollo, empaquetado y ejecución de aplicaciones en contenedores. Permite desacoplar las aplicaciones de la infraestructura subyacente, facilitando la entrega rápida y reproducible del *software*. Se ha seleccionado como motor de contenerización subyacente. Se ha optado por OrbStack frente a clientes tradicionales como Docker Desktop por su arquitectura ligera y su mayor eficiencia en la gestión de CPU y memoria en entornos macOS con Apple Silicon.

### 2.2 Kubernetes (Minikube)

Kubernetes es un sistema de orquestación de contenedores de código abierto que permite automatizar el *deployment*, la gestión y el escalado de aplicaciones. Se ha implementado como el orquestador central del ecosistema, optando por Minikube para el entorno local. Se ha descartado el *deployment* monolítico tradicional en favor de Kubernetes por su capacidad nativa para la gestión de *clusters*, el enrutamiento interno mediante Services, la autorreparación (*self-healing*) de *pods* en mal estado y la escalabilidad horizontal. Constituye el núcleo de la alta disponibilidad del sistema.

---

## B. Capa de Aplicación y Persistencia

### 2.3 Node.js (*Backend*)

Node.js ha sido elegido como tecnología de *backend* por su arquitectura orientada a eventos y su modelo de entrada/salida no bloqueante (asíncrono). Es la tecnología idónea para construir microservicios ligeros que actúan como APIs RESTful, capaces de manejar múltiples conexiones concurrentes sin consumir un volumen excesivo de recursos del *cluster*.

### 2.4 Nginx (*Frontend* / Ingress Controller)

Nginx actúa como *proxy* inverso y servidor de contenido estático. Su ligereza y su bajo consumo de memoria lo convierten en el candidato idóneo tanto para servir la interfaz de usuario web como para gestionar el enrutamiento y el balanceo de carga del tráfico entrante (*ingress*) hacia los distintos servicios internos del *cluster*.

### 2.5 PostgreSQL

PostgreSQL ha sido seleccionado como base de datos relacional por su estricto cumplimiento de las propiedades ACID (Atomicidad, Consistencia, Aislamiento, Durabilidad). En un entorno distribuido y volátil como Kubernetes, contar con una base de datos fiable garantiza la integridad y la persistencia de los datos —*leads* o clientes— frente a posibles reinicios de los *pods* de la aplicación.

---

## C. Capa de *Deployment* y Automatización (Filosofía *GitOps*)

### 2.6 GitHub (Sistema de Control de Versiones)

GitHub actúa como el repositorio central del código fuente y, bajo el paradigma *GitOps*, como la «Única Fuente de la Verdad» (*Single Source of Truth*) para el estado de la infraestructura. Cualquier modificación del *cluster* debe pasar obligatoriamente por un *commit* auditable en este repositorio.

### 2.7 GitHub Actions (Integración Continua — CI)

GitHub Actions es el motor de automatización utilizado para la fase de construcción. Cada cambio en la rama principal (`main`) desencadena un *pipeline* que compila y empaqueta el nuevo código en imágenes Docker, publicándolas en el registro de contenedores.

### 2.8 GitHub Container Registry

GHCR (*GitHub Container Registry*) actúa como el almacenamiento seguro donde se depositan las imágenes Docker recién compiladas por la fase de CI. Constituye el puente logístico entre la construcción del código y su posterior descarga por parte de Kubernetes.

### 2.9 ArgoCD (Despliegue Continuo — CD)

ArgoCD es la pieza central de la estrategia *GitOps*. En lugar de ejecutar comandos manuales, monitoriza el repositorio de GitHub en busca de cambios en los manifiestos YAML y aplica (*pull*) la configuración hacia el *cluster*. Si alguien modifica el *cluster* de forma manual, ArgoCD detecta la discrepancia (*drift*) y devuelve automáticamente el sistema al estado definido en Git.

### 2.10 SealedSecrets (Bitnami)

SealedSecrets resuelve el problema crítico de la gestión de secretos en *GitOps*. Dado que los manifiestos se almacenan en repositorios Git, subir contraseñas en texto plano o codificadas en Base64 supone una vulnerabilidad crítica. SealedSecrets utiliza criptografía asimétrica para cifrar las contraseñas, permitiendo que únicamente el *cluster* de Kubernetes —con su clave privada— pueda descifrarlas e inyectarlas en los contenedores.

---

## D. Capa de Seguridad y Observabilidad

### 2.11 Prometheus y Grafana (Monitorización)

Prometheus actúa como base de datos de series temporales que extrae métricas de consumo (CPU, RAM, red) de todos los nodos y contenedores del *cluster*. Grafana se conecta a Prometheus para visualizar esos datos en paneles en tiempo real. Esta capa demuestra la capacidad no solo de desplegar la infraestructura, sino también de operarla, monitorizarla y anticiparse a posibles cuellos de botella técnicos.

### 2.12 mkcert (Certificados TLS Locales)

mkcert es una herramienta de línea de comandos que genera una autoridad de certificación (CA) local e instala el certificado raíz en el almacén de confianza del sistema operativo (Keychain en macOS). Permite emitir certificados TLS para dominios locales (`.test`) que los navegadores reconocen como válidos sin mostrar advertencias de seguridad. Los certificados generados se cifran con SealedSecrets antes de almacenarse en el repositorio.

### 2.13 K6

K6 es una herramienta *open source* de pruebas de carga escrita en Go, con *scripts* en JavaScript. Permite simular usuarios virtuales que realizan peticiones HTTP concurrentes contra la plataforma para validar su comportamiento bajo carga. Se ha empleado para verificar que la arquitectura soporta tráfico real, definiendo métricas personalizadas (contadores de errores y *trends* de duración por *endpoint*) que se consolidan en el informe final emitido por la propia herramienta.

---

## Resumen del *stack* tecnológico

| Capa | Tecnología | Función |
|------|-----------|---------|
| Orquestación | Kubernetes (Minikube) | Gestión de contenedores |
| *GitOps* CD | ArgoCD | *Deployment* continuo desde Git |
| CI | GitHub Actions | Construcción y publicación de imágenes |
| Registro | GitHub Container Registry | Almacenamiento de imágenes Docker |
| *Frontend* | Nginx | Servidor de estáticos y *proxy* inverso |
| *Backend* | Node.js + Express | API REST (`POST /api/leads`) |
| Base de datos | PostgreSQL 15 | Persistencia (PVC 1 GiB) |
| Administración DB | pgAdmin 4 | Interfaz web para PostgreSQL |
| Secretos | SealedSecrets (Bitnami) | Cifrado asimétrico de secretos en Git |
| TLS | mkcert + SealedSecrets | Certificados de confianza local |
| Monitorización | Prometheus + Grafana | Métricas de *pods*, nodos y red |
| Ingress | NGINX Ingress Controller | Enrutamiento HTTP/HTTPS |

---

*Siguiente: [3.1–3.2 Arquitectura y Namespaces →](03-arquitectura.md)*
