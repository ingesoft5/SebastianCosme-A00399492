# Bitácora de Colaboración con IAG

## 1. Bitácora de prompts con la herramienta de IA generativa[cite: 10]

| Fase del Taller | Prompt / Interacción Principal | Tipo de Ayuda Solicitada[cite: 10] |
| :--- | :--- | :--- |
| **Fase 1** | Análisis de la estructura del directorio y validación de versiones de stack (Java, React, etc.). | Explicación y validación estructural. |
| **Fase 1** | Solicitud de comandos para ejecutar pruebas con Maven y build del frontend. | Generación de configuración. |
| **Fase 1** | Solución al error `sh: 1: tsc: not found` al construir el frontend con Vite. | Depuración. |
| **Fase 2** | Paso a paso para empaquetamiento inmutable (`Dockerfile` multi-stage) y publicación en Nexus. | Generación de configuración. |
| **Fase 2** | Ajuste de `Dockerfile` para aplicar la "Buena Práctica" de caché (`go-offline`) basada en diapositivas de clase. | Refinamiento y explicación. |
| **Fase 2** | Resolución de error al hacer build del frontend por mezcla accidental de configuraciones de Java. | Depuración. |
| **Fase 2** | Solución al error HTTP 400 en Nexus (`RELEASE does not allow version: 0.0.1-SNAPSHOT`) al subir el `.jar`. | Depuración y explicación de políticas. |
| **Fase 3** | Configuración de orquestación con Docker Compose para Nexus, Jenkins y Smee. | Generación de configuración. |
| **Fase 3** | Explicación sobre el error de comando no encontrado al usar `docker-compose` (v1) en lugar de `docker compose` (v2). | Explicación. |
| **Fase 3** | Corrección de error de validación YAML en la directiva `healthcheck.retries` de Nexus. | Depuración. |
| **Fase 3** | Configuración del contenedor de Smee (`SMEE_TARGET`) y archivo `.env` para enlazar GitHub Webhooks. | Generación de configuración. |
| **Fase 3** | Error en instalación de plugins de Jenkins (`credentials-binding`, `junit`) por versiones obsoletas en `plugins.txt`. | Depuración. |
| **Fase 4** | Generación del pipeline declarativo final (`Jenkinsfile`) orquestando Checkout, Build, Nexus Push y Smoke Test. | Generación de configuración. |

## 2. Párrafo de reflexión[cite: 10]

Durante el desarrollo de este taller evaluativo de integración y despliegue continuo (CI/CD), la Inteligencia Artificial Generativa actuó como un asistente técnico de alto valor, aportando agilidad en la escritura de sintaxis repetitiva y andamiaje para configuraciones complejas, como la estructuración de los archivos `docker-compose.yml`, los `Dockerfiles` multi-stage y el bloque inicial del `Jenkinsfile` declarativo[cite: 10]. Asimismo, su capacidad de análisis fue vital para destrabar bloqueos en tiempo de ejecución; por ejemplo, identificando rápidamente el conflicto de políticas en Nexus que rechazaba el sufijo `-SNAPSHOT` de Maven, y resolviendo los fallos de instalación en Jenkins causados por la especificación de versiones obsoletas en el archivo `plugins.txt`[cite: 10]. 

Sin embargo, la herramienta presentó limitaciones inherentes a su falta de visibilidad del entorno de ejecución local[cite: 10]. Al no tener acceso directo al sistema de archivos, la IAG cometió errores de contexto (como sugerir configuraciones de backend dentro del Dockerfile del frontend) y requirió de mi intervención constante para proveer la salida exacta de la consola ante cada fallo[cite: 10]. Como ingeniero a cargo de la solución, tomé decisiones técnicas de forma independiente que definieron el rumbo del proyecto: fui yo quien validó la arquitectura real contra la propuesta de la IA, decidí anular la sugerencia inicial del `Dockerfile` para forzar una implementación más estricta de caché de dependencias de Maven basada en los estándares vistos en clase, y me encargué de la gestión crítica de seguridad manejando localmente mis archivos de credenciales (`~/.m2/settings.xml`) y la interconexión de webhooks con Smee.io[cite: 10]. Este proceso reafirmó que la IAG es excelente acelerando la codificación, pero el control arquitectónico, la comprensión del ecosistema y la validación final dependen de mi criterio técnico[cite: 10].