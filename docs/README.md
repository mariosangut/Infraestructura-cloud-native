---
title: Inicio — TFG Cloud Native GitOps
description: Documentación técnica del Proyecto Intermodular de CFGS ASIR — Kubernetes, ArgoCD, GitOps, Prometheus, SealedSecrets
tags: kubernetes, gitops, argocd, ci-cd, tfg
---

# Plataforma Cloud Native · GitOps

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-F4653D?style=for-the-badge&logo=argo&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2671E5?style=for-the-badge&logo=githubactions&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white)

> Documentación técnica completa del Proyecto Intermodular de Grado Superior — CFGS ASIR · I.E.S. Zaidín-Vergeles · Curso 2025-2026

---

## Resumen ejecutivo

Este proyecto implementa una plataforma de infraestructura *cloud native* completa sobre Kubernetes, gestionada bajo el paradigma *GitOps*. Todo el estado del *cluster* se declara en este repositorio y es reconciliado de forma continua y automática por ArgoCD.

| Área | Tecnología |
|------|-----------|
| Orquestación | Kubernetes (Minikube) |
| *GitOps* CD | ArgoCD |
| CI | GitHub Actions + Docker Buildx (*multi-arch*) |
| Registro | GitHub Container Registry |
| *Frontend* | Nginx (3 réplicas) |
| *Backend* | Node.js + Express (HPA 1-5 réplicas) |
| Base de datos | PostgreSQL 15 (PVC 1 GiB) |
| Secretos | SealedSecrets (Bitnami) |
| TLS | mkcert + SealedSecrets |
| Monitorización | Prometheus + Grafana (kube-prometheus-stack) |

---

## Estructura de la documentación

| Sección | Contenido |
|---------|----------|
| [1. Definición del Proyecto](01-definicion.md) | Objetivos, motivación y alcance |
| [2. Tecnologías Utilizadas](02-tecnologias.md) | Descripción del *stack* tecnológico |
| [3.1–3.2 Arquitectura y Namespaces](03-arquitectura.md) | Diseño de la arquitectura y aislamiento lógico |
| [3.3 Flujo GitOps y CI/CD](04-gitops-ci.md) | Pipeline CI/CD y patrón *App of Apps* |
| [3.3.3–3.3.4 Seguridad y TLS](05-seguridad.md) | SealedSecrets y certificados TLS |
| [3.4 Observabilidad y Pruebas](06-observabilidad.md) | Prometheus, Grafana, K6 y HPA |
| [3.5 Modelo de Datos y API](07-api.md) | Esquema de BD y contrato REST |
| [4. Anexos](08-anexos.md) | Manifiestos, *workflows*, capturas |
| [5. Recursos Bibliográficos](09-bibliografia.md) | Referencias documentales |

---

*Mario Sánchez Gutiérrez · I.E.S. Zaidín-Vergeles · 2025-2026*
