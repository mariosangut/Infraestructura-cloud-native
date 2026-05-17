# 1. Definición del Proyecto

Este proyecto consiste en el diseño, despliegue y administración de una arquitectura de infraestructura *cloud native* basada en el modelo de microservicios. Utilizando Kubernetes como motor de orquestación, se ha construido un entorno de alta disponibilidad que aloja una aplicación web completa —separada en *frontend* y *backend*— respaldada por una base de datos relacional PostgreSQL. La infraestructura se gestiona en su totalidad bajo la filosofía *GitOps*, delegando la gestión del estado del *cluster* en repositorios versionados. El objetivo general del presente trabajo es implementar un ciclo de vida de desarrollo y operaciones (DevOps) completamente automatizado y seguro.

## Objetivos específicos

- Automatizar la construcción de imágenes y el *deployment* continuo (CI/CD) sin intervención humana mediante GitHub Actions y ArgoCD.
- Garantizar la seguridad de las credenciales y configuraciones sensibles del *cluster* mediante criptografía asimétrica (SealedSecrets).
- Proveer observabilidad total y en tiempo real del estado de los nodos, *pods* y consumo de red mediante el despliegue de un *stack* de monitorización profesional (Prometheus y Grafana).
- Asegurar el enrutamiento eficiente del tráfico externo hacia los servicios internos mediante el controlador Ingress.

## Flujo de entrega automatizado

El objetivo inicial era construir un ciclo de entrega completamente automatizado: al realizar un `git push` en la rama `main`, el *pipeline* de GitHub Actions construye la imagen, la publica en GHCR y actualiza el manifiesto en la rama `gitops`. ArgoCD, que monitoriza esta última rama, detecta el cambio y actualiza el *cluster* sin requerir ninguna acción adicional por parte del administrador.

---

*Siguiente: [2. Tecnologías Utilizadas →](02-tecnologias.md)*
