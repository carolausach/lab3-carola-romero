# Laboratorio 3 - Despliegue CI/CD en Kubernetes

**Estudiante:** Carola Romero  
**Aplicación:** NestJS  
**Versión:** 3.0.0

## Descripción

Este proyecto implementa un flujo CI/CD para una aplicación NestJS utilizando Docker, Docker Hub, Jenkins y Kubernetes.

El pipeline de Jenkins utiliza agentes dinámicos desplegados como Pods dentro del clúster Kubernetes y automatiza las etapas de instalación, pruebas, compilación, publicación de la imagen Docker y despliegue.

## Repositorio Docker

Imagen utilizada:

`carolaromero/lab3-carola-romero:carola-romero`

También se publica la versión:

`carolaromero/lab3-carola-romero:3.0.0`

## Recursos Kubernetes

Los recursos utilizados por la aplicación son:

- Namespace: `ns-carola-romero`
- Deployment: `app-carola-romero`
- Service: `svc-carola-romero`
- ConfigMap: `config-carola-romero`
- Secret: `secret-carola-romero`
- Réplicas: `2`

El Deployment utiliza la imagen:

`carolaromero/lab3-carola-romero:carola-romero`

## Variables de entorno

La aplicación utiliza las siguientes variables:

- `AMBIENTE`, obtenida desde `config-carola-romero`
- `API_KEY`, obtenida desde `secret-carola-romero`

## Construcción manual con Docker

Construir la imagen:

```bash
docker build -t carolaromero/lab3-carola-romero:carola-romero .
