# Introduccion

* **Prometheus** es un **sistema de monitoreo y recolección de métricas** *open source*, creado originalmente por **SoundCloud**y hoy parte de la **Cloud Native Computing Foundation (CNCF)**

* Su funcion principal es:

    > *Recolectar, almacenar y consultar métricas de sistemas y apliaciones en tiempo real*
    
    * No es para logs ni trazas (eso es otro cuento), **Prometheus es para metricas**

    ## Para que sirve Prometheus ?

    * **Monitorear aplicaciones**

        * Uso de CPU, Memoria, Disco
        * Latencia de endpoints
        * Cantidad de request
        * Errores (5xx, 4xx)

    * **Monitorear infraestructura**

        * Servidores
        * Contenedores
        * Pods y nodos de Kubernetes

    * **Detectar problemas antes de que exploten**

        * Picos de CPU
        * Servicios caídos
        * Respuestas lentas

    * **Alerta automáticamente**

        * *Este servicio lleva 5 minutos caido*
        * *La memoria está al 90%*

    ## Funcionamiento

    * Prometheus funciona como un **modelo Pull** (esto es Muy importante)

    * **Flujo general:**

        ```bash
        [Aplicación / Sistema]
            ↓ expone métricas
            /metrics (HTTP)
                ↓
        [Prometheus]
                ↓
        Guarda métricas
                ↓
        Consulta / Alertas / Grafana
        ```
        1. **Tu aplicación expone métricas**

            * En un endpoint HTTP (ej: `/metrics`)
            * Ejemplo: Spring Boot, FastAPI, Node, etc
        
        2. **Prometheus *raspa* (scrape) las métricas**

            * Cada X segundos (ej: cada 15s)
            * Hace un GET a `/metrics`

        3. **Almacena las métricas**

            * En su base de datos interna
            * Como **series temporales**

        4. **Consulta las métricas**

            * Usando su lenguaje: **PromQL**

        5. **Dispara alertas**

            * Si una condición se cumple (CPU alta, servicio caído, etc)

    ## Elementos que componen Prometheus

    * Ahora lo importante: **Las piezas internas**

    1. **Prometheus Server**

        * Es el **corazon** Contiene:

            * **Scraper** -> recoge métricas
            * **TSDB** (Time Series Database) -> guarda métricas
            * **PromQL Engine** -> permite consultar datos

        * Ejemplo de métrica almacenada:

            ```bash
            http_request_total{method='GET', status='200'} 10234
            ```
    
    2. **Métricas (Metrics)**

        * Son valores **numericos** que cambian en el tiempo

        * Tipos principales

            | Tipo      | Descripción  | Ejemplo             |
            | --------- | ------------ | ------------------- |
            | Counter   | Solo aumenta | Requests totales    |
            | Gauge     | Sube y baja  | Uso de CPU          |
            | Histogram | Distribución | Latencias           |
            | Summary   | Percentiles  | Tiempo de respuesta |

    3. **Exporters**

        * No todo expone métricas por defecto

        * Los **exporters** son *adaptadores* que convierten métricas a formato Prometheus

        * Ejemplo:

            * `node_exporter` -> métricas del servidor
            * `kube-state-metrics` -> estado de Kubernetes
            * `postgres_exporter` -> métricas de PosgreSQL

                ```bash
                Servidor -> node_exporter -> /metrics Prometheus
                ```
        
    4. **Targets**

        * Son los **objetivos** que Prometheus va a monitorear

        * Ejemplo

            * Un pod
            * Un servicio
            * Un nodo
            * Una base de datos

        * En config

            ```yaml
            scrape_configs:
              - job_name: "api-spring"
                static_configs:
                  - targets: ["api:8080"]
            ```

    5. **Service Discovery**

        * Prometheus descruber **automáticamente** los targets
        * Muy usado en Kubernetes:

            * Pods 
            * Services
            * Endpoints
        
        * No necesitas IPs fijas

    6. **PromQL** (Prometheus Query Language)

        * Languaje para consultar métrica

        * Ejemplo:

            * Request por segundo:

                ```promql
                rate(http_request_total[1m])
                ```
            
            * CPU promedio:

                ```promql
                avg(node_cpu_seconds_total)
                ```
    7. **Alertmanager**

        * Es el encargado de **gestionar alertas**

        * Funciones

            * Agrupar alertas
            * Enviar notificaciones
            * Evitar spam

        * Canales 

            * Email
            * Slack
            * Telegram
            * Webhooks

        * Ejemplo:

            > *El servicio `inventario` esta caido hace 3 minutos*

    8. **Grafana (Complemento, no parte directa)**

        > No es parte de Prometheus, pero siempre van juntos

        * Grafana sirve para:

            * Dashboards visuales
            * Gráficas
            * Paneles de monitoreo

            ```nginx
            Prometheus -> Grafana -> Dashboard
            ```
    ## Resumen

    | Componente        | Función                     |
    | ----------------- | --------------------------- |
    | Prometheus Server | Recolecta y guarda métricas |
    | Exporters         | Exponen métricas            |
    | Targets           | Qué se monitorea            |
    | PromQL            | Consultar métricas          |
    | Alertmanager      | Manejo de alertas           |
    | Grafana           | Visualización               |
