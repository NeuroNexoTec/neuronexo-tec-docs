# Modelo estandar

El modelo estructurado de error, o modelo estándar, es la representación formal y consistente de un error en una aplicación. Su objetivo principal es proporcionar una forma coherente de describir, transportar, almacenar y visualizar errores en todos los niveles del sistema. Este modelo actúa como un lenguaje común entre todas las capas —infraestructura, dominio, aplicación, presentación y observabilidad— y entre todos los componentes involucrados en el ciclo de vida del software: desarrolladores, consumidores, operadores, testers, monitoreo automatizado y más.

Un modelo estándar de error no sólo encapsula el mensaje de fallo, sino que estructura la información de manera que sea entendible, trazable, segura y útil para la toma de decisiones técnicas y de negocio. Su implementación adecuada permite que los errores sean:

- Clasificados automáticamente
- Correlacionados entre servicios distribuidos
- Interpretados por humanos y máquinas
- Visualizados en dashboards
- Auditablemente documentados

## Campos del modelo estandar
---

A continuación se define la estructura base del modelo estándar, junto con la justificación, utilidad y aplicación de cada campo:

| Campo          | Descripción                                                                                                 | Utilidad y Aplicación                                                                                             |
| -------------- | ----------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `timestamp`    | Fecha y hora en que ocurrió el error (formato UTC ISO 8601)                                                 | Permite auditoría temporal, correlación de eventos y análisis forense. Fundamental para trazabilidad y monitoreo. |
| `error_code`   | Identificador único y legible del tipo de error (ej. `USER_NOT_FOUND`, `DB_TIMEOUT`, `VALIDATION_ERROR`)    | Facilita identificación rápida, clasificación automatizada y lógica condicional por tipo de error.                |
| `message`      | Descripción amigable del error, apta para mostrar a usuarios o desarrolladores                              | Es la interfaz humana del error. Puede internacionalizarse o versionarse para múltiples consumidores.             |
| `status`       | Código numérico del error (HTTP status code, código interno o estándar empresarial)                         | Proporciona semántica formal sobre el tipo de error. Útil en APIs HTTP y respuestas genéricas.                    |
| `severity`     | Nivel de severidad (`INFO`, `WARNING`, `ERROR`, `CRITICAL`)                                                 | Permite filtros operativos, reglas de alerta, prioridades de resolución.                                          |
| `trace_id`     | Identificador único de trazabilidad entre servicios/disparadores                                            | Clave para seguimiento en sistemas distribuidos y observabilidad avanzada (tracing).                              |
| `context`      | Datos adicionales del entorno donde ocurrió el error (ej. nombre del servicio, nombre del usuario, entorno) | Permite enriquecer el error para entender su causa raíz sin comprometer seguridad.                                |
| `stack_trace`  | Traza de la pila de ejecución (internamente disponible pero nunca expuesta al cliente externo)              | Útil para debugging profundo, solo accesible por logs seguros.                                                    |
| `details`      | Lista de detalles estructurados, útil en errores de validación o errores compuestos                         | Mejora la experiencia del desarrollador al mostrar exactamente qué falló y por qué.                               |
| `origin_layer` | Capa donde se originó el error (`infrastructure`, `domain`, `application`, `presentation`)                  | Ayuda a identificar responsabilidad y clasificar errores para diagnóstico.                                        |
| `retryable`    | Indica si el error es transitorio y puede reintentarse (`true` / `false`)                                   | Fundamental para flujos asincrónicos, resiliencia y sistemas con reintentos automáticos.                          |
| `category`     | Clasificación semántica (`validation`, `business`, `technical`, `unknown`)                                  | Permite análisis agregados y reglas específicas por tipo de error.                                                |

## Aplicacion del modelo por capas
---

### Capa de infraestructura

La capa de infraestructura representa la base técnica que sustenta el funcionamiento de una aplicación: conexiones a bases de datos, servicios externos, colas de mensajes, sistemas de archivos, redes, almacenamiento, balanceadores, proveedores cloud, contenedores, entre otros. Dada su naturaleza, esta capa está particularmente expuesta a fallos operacionales, dependencias externas, problemas de disponibilidad o configuración, y eventos de red.

Implementar un modelo estructurado de error en esta capa no es solo deseable, es crítico. Una falla en infraestructura puede desencadenar errores en cascada si no se detecta, clasifica y trata correctamente. Este capítulo detalla cómo aplicar el modelo estándar de error en este contexto, qué tipo de errores son comunes, cómo manejarlos y cómo integrarlos adecuadamente al sistema general de errores.

#### 1. Tipos de errores comunes en la capa de infraestructura

a) Errores de red

- Timeout al conectarse a un servicio externo
- DNS mal resuelto
- Desconexión durante la transmisión de datos

b) Errores de base de datos

- Conexión rechazada
- Query malformado
- Constraint violations
- Timeout o locks

c) Errores en almacenamiento

- Permisos insuficientes en disco
- Espacio insuficiente
- Falla al leer/escribir archivo
- Path no encontrado

d) Errores de servicios externos

- Respuestas mal formadas (mal JSON/XML)
- Errores de autenticación (401, 403)
- Errores de disponibilidad (503, timeout)
- Cambios en APIs de terceros

e) Errores de configuración

- Variables de entorno faltantes o inválidas
- Certificados expirados
- Recursos mal definidos

f) Errores del sistema operativo

- Procesos que no pueden iniciarse
- Problemas de permisos
- Límites de sistema alcanzados (open files, memory)

g) Errores en colas o brokers de mensajes

- Fallas al publicar/consumir mensajes
- Problemas de conectividad con RabbitMQ, Kafka, etc.
- Problemas de particionado o desincronización

La infraestructura actúa como proveedor de capacidades al dominio, la aplicación o la presentación. Por tanto, es esta capa la que detecta los errores técnicos y los convierte en errores con semántica útil.

Aquí es donde se genera el error interno estructurado, que luego será propagado, transformado o loggeado.


Ejemplo:
```json
{
  "timestamp": "2025-06-07T14:02:45Z",
  "error_code": "DB_CONNECTION_TIMEOUT",
  "message": "No se pudo conectar con la base de datos",
  "status": 500,
  "severity": "CRITICAL",
  "trace_id": "abc-123-def-456",
  "origin_layer": "infrastructure",
  "retryable": true,
  "category": "technical"
}
```

Importancia: Permite que errores técnicos no lleguen directamente a la capa de presentación. La lógica de negocio no necesita conocer detalles del proveedor (por ejemplo, que es PostgreSQL o MongoDB).

### Capa de dominio

Errores que representan reglas de negocio violadas.

Ejemplo:
```json
{
  "timestamp": "2025-06-07T14:05:10Z",
  "error_code": "INSUFFICIENT_FUNDS",
  "message": "El saldo disponible no es suficiente para completar la operación",
  "status": 422,
  "severity": "ERROR",
  "trace_id": "tx-9988-cc77",
  "origin_layer": "domain",
  "retryable": false,
  "category": "business"
}
```
Importancia: No tiene sentido reintentar este error. Es decisión de negocio, no un fallo técnico.

### Capa de aplicacion

Errores en la orquestación de casos de uso, errores derivados de invocaciones fallidas o lógica de aplicación.

Ejemplo:

```json
{
  "error_code": "EMAIL_DISPATCH_FAILED",
  "message": "No fue posible enviar el correo de confirmación",
  "status": 500,
  "origin_layer": "application",
  "category": "technical",
  "retryable": true
}
```
Importancia: Este error puede necesitar fallback o reintento por parte de un worker asíncrono.

### Capa de presentacion

Errores que afectan la forma en que se comunica el sistema con el usuario final.

Ejemplo (en una API HTTP):

```json
{
  "error_code": "MISSING_REQUIRED_FIELDS",
  "message": "Algunos campos requeridos no fueron proporcionados",
  "status": 400,
  "origin_layer": "presentation",
  "details": [
    { "field": "email", "issue": "Campo obligatorio" },
    { "field": "password", "issue": "Campo obligatorio" }
  ],
  "category": "validation"
}
```

Importancia: Permite mostrar mensajes útiles y reutilizar UI para mostrar errores de entrada.

### Capa de observabilidad
En esta capa, el modelo se transforma en logs, métricas o trazas.

- Se registran errores con su severity, trace_id y context
- Se generan métricas como error_count{category="technical"} o validation_failure_rate
- Se integran con sistemas como Prometheus, Grafana, Elastic, Sentry, Datadog o similares

## Contextos de aplicacion del modelo

### 1. Flujos HTTP
El modelo se serializa como respuesta JSON

- Se define una convención para códigos de estado (200, 400, 404, 422, 500)
- Se incluye el trace_id como header para seguimiento entre microservicios

Ejemplo: Una API REST que devuelve un 422 Unprocessable Entity con errores de validación detallados

### 2. Flujos Asíncronos (colas, pub/sub)
El modelo se incluye en el payload del mensaje de error

- Se puede incluir el error en una cola de “dead-letter” para ser revisado
- El retryable: true se usa para decidir si reenviar el mensaje o escalar

Ejemplo: Un worker de procesamiento de imágenes que falla y publica el error en una cola de fallos.

### 3. Conexiones por Socket (WebSocket, TCP, etc.)
El error se envía como mensaje estructurado

- Puede incluir un campo correlation_id si el flujo tiene peticiones-respuestas asíncronas

Ejemplo:

```json
{
  "type": "error",
  "correlation_id": "msg-xyz123",
  "payload": {
    "error_code": "INVALID_COMMAND",
    "message": "El comando recibido no está soportado por el servidor",
    "status": 400,
    "trace_id": "trace-abc999"
  }
}
```
### 4. Flujos internos y batch
Los errores se almacenan en logs o bases de datos internas

- El modelo se usa para construir reportes de ejecución (por lote o por entrada)
- Puede usarse para crear dashboards de procesos fallidos

### 5. Sistemas embebidos o CLI
El modelo puede reducirse a una estructura mínima (code, message, timestamp)
El log debe incluir trace_id para diagnósticos posteriores

## Cómo Implementar el Modelo Estándar
Definir el esquema estructurado (JSON Schema, clase base, interfaz)

Establecer nomenclatura y codificación de errores (MODULE_ACTION_FAILURE)

Agregar generación automática de trace_id y timestamp

Instrumentar el sistema para generar errores estructurados en todas las capas

Estandarizar los logs y exportadores de errores

Educar a los equipos de desarrollo sobre cómo construir y lanzar errores estándar

Retroalimentación y Complemento del Capítulo
Este capítulo define con precisión la estructura y propósito del modelo estándar, cubriendo todos los campos esenciales y sus usos por capas y contextos. No obstante, para una implementación completa en entornos reales, se recomienda extender este capítulo incluyendo:

Una matriz de mapeo de errores comunes por capa

Políticas para evitar duplicidad de errores y códigos

Reglas para construir mensajes localizables y seguros

Prácticas de versionado del modelo en sistemas evolucionables

Integración con circuit breakers, rate limits y fallback policies

Además, se podría incluir una herramienta de validación (como JSON schema validator o linters internos) para asegurar que todos los errores lanzados cumplen con el formato estandarizado.