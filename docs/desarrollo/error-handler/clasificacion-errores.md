# Clasigicacion de errores

## Errores de infraestructura

``ERR-INF-001`` – Connection Refused

**Descripción**: La conexión a un host/puerto remoto fue rechazada porque no hay servicio escuchando o el acceso fue bloqueado por firewall o política de red.

**Síntomas**: 

- Mensaje de error del tipo ECONNREFUSED.

**Identificación**: Logs de cliente de red, error inmediato al intentar conexión.

**Origen**: Infraestructura.

---

``ERR-INF-002`` – Host Unreachable

**Descripción**: No se puede alcanzar el host remoto. Puede deberse a que el host está fuera de línea o la red no tiene ruta.

**Síntomas**: 

- No route to host
- Destination unreachable.

**Identificación**: Ping fallido, traceroute incompleto.

**Origen**: Infraestructura.

---

``ERR-INF-003`` – Timeout de Conexión

**Descripción**: El cliente no pudo conectarse al servicio remoto dentro del tiempo permitido.

**Síntomas**: 

- Connect ETIMEDOUT 
- Socket timeout.

**Identificación**: Se observa demora y posterior error en la petición.

**Origen**: Infraestructura.

---

``ERR-INF-004`` – DNS Resolution Failed

**Descripción**: Fallo al resolver un nombre de dominio a una dirección IP.

**Síntomas**: 

- ENOTFOUND
- DNS_PROBE_FINISHED_NXDOMAIN.

**Identificación**: Verificación mediante dig, nslookup.

**Origen**: Infraestructura.

---

``ERR-INF-005`` – Conexión Cerrada Prematuramente

**Descripción**: La conexión fue cerrada por el servidor antes de que se completara la comunicación.

**Síntomas**: 

- Connection reset by peer
- Broken pipe.

**Identificación**: Se produce durante transferencia activa.

**Origen**: Infraestructura.

---


``ERR-INF-006`` – No Space Left on Device

**Descripción**: El sistema de archivos se ha quedado sin espacio.

**Síntomas**: 

- ENOSPC 
- Disk quota exceeded.

**Identificación**: Comando df -h o equivalente.

**Origen**: Infraestructura.

---

``ERR-INF-007`` – Permission Denied

**Descripción**: Permiso insuficiente para acceder, crear o modificar un archivo o directorio.

**Síntomas**: 

- EACCES 
- Unauthorized access.

**Identificación**: Error lanzado al intentar leer/escribir.

**Origen**: Infraestructura.

---

``ERR-INF-008`` – File Not Found

**Descripción**: El archivo o ruta especificada no existe.

**Síntomas**:

- ENOENT
- FileNotFoundException.

**Identificación**: Ruta inválida, typo o eliminación previa.

**Origen**: Infraestructura.

---

``ERR-INF-009`` – Archivo Corrupto o Inválido

**Descripción**: El archivo está dañado o no puede interpretarse correctamente.

**Síntomas**: 

- Checksum mismatch 
- Invalid file header

**Identificación**: Validación de checksum, errores de parsing.

**Origen**: Infraestructura.

---


``ERR-INF-010`` – SQL Syntax Error

**Descripción**: Error en la sintaxis de una consulta SQL.

**Síntomas**: 

- Syntax error
- Unexpected token.

**Identificación**: Validar SQL emitido.

**Origen**: Infraestructura.

---

``ERR-INF-011`` – Constraint Violation

**Descripción**: Violación de restricciones de integridad (clave única, foránea, etc.).

**Síntomas**: 

- Duplicate key
- Foreign key violation.

**Identificación**: Código de error SQL como 23505.

**Origen**: Infraestructura.

---

``ERR-INF-012`` – Connection Pool Exhausted

**Descripción**: No hay conexiones disponibles en el pool de base de datos.

**Síntomas**: 

- Timeout waiting for connection 
- Max pool size reached.

**Identificación**: Monitoreo de pool.

**Origen**: Infraestructura.

---

``ERR-INF-013`` – Deadlock Detected

**Descripción**: Dos transacciones compiten por los mismos recursos generando un ciclo de espera.

**Síntomas**: 

- Deadlock found when trying to get lock.

**Identificación**: Logs del motor SQL, traza de locks.

**Origen**: Infraestructura.

---

``ERR-INF-014`` – Authentication Failed

**Descripción**: Credenciales incorrectas o faltantes al intentar conectarse.

**Síntomas**: 

- Access denied 
- Authentication failed.

**Identificación**: Verificación de variables/env config.

**Origen**: Infraestructura.

---

``ERR-INF-015`` – Key Not Found

**Descripción**: Se intenta leer un documento o registro que no existe.

**Síntomas**: 

- 404 not found
- null result.

**Identificación**: Verificación manual de la clave o ID.

**Origen**: Infraestructura.

---

``ERR-INF-016`` – Cluster Unavailable

**Descripción**: El clúster de base de datos NoSQL no está disponible o no hay nodo maestro activo.

**Síntomas**: 

- No reachable nodes 
- Cluster is down.

**Identificación**: Monitor del clúster, nodos offline.

**Origen**: Infraestructura.

---

``ERR-INF-016`` – Timeout en Operación

**Descripción**: Tiempo de espera excedido durante una operación (lectura/escritura).

**Síntomas**: 

- QueryTimeoutException
- Read timed out.

**Identificación**: Latencias altas, problemas de red o bloqueo de disco.

**Origen**: Infraestructura.

---

``ERR-INF-017`` – Formato de Documento Inválido

**Descripción**: Documento no cumple con el esquema esperado (en bases con validaciones).

**Síntomas**: 

- Validation failed
- Schema violation.

**Identificación**: Error estructural al insertar/updatear.

**Origen**: Infraestructura.

---

``ERR-INF-018`` – Exceeded Document Size Limit

**Descripción**: Se excede el tamaño máximo permitido para un documento.

**Síntomas**: 

- Document too large 
- BSONObj size.

**Identificación**: Configuración del motor (e.g. MongoDB limita a 16MB).

**Origen**: Infraestructura.

---

``ERR-INF-019`` – Broker Not Available

**Descripción**: El broker de mensajes está fuera de línea o no responde.

**Síntomas**: 

- Kafka broker unavailable 
- RabbitMQ timeout.

**Identificación**: Logs del broker, verificación de conectividad.

**Origen**: Infraestructura.

---

``ERR-INF-020`` – Message Too Large

**Descripción**: El mensaje enviado supera el tamaño máximo permitido.

**Síntomas**: 

- MessageSizeExceededException.

**Identificación**: Revisar configuración del tópico/cola.

**Origen**: Infraestructura.

---

``ERR-INF-021`` – Offset Out of Range

**Descripción**: El consumidor intenta leer desde un offset inexistente.

**Síntomas**: 

- OffsetOutOfRangeException.

**Identificación**: Monitor de consumidor.

**Origen**: Infraestructura.

---

``ERR-INF-022`` – Connection Timeout to Queue

**Descripción**: No se pudo establecer conexión con la cola.

**Síntomas**: 

- Connection refused
- Timeout.

**Identificación**: Revisión de red, TLS, autenticación.

**Origen**: Infraestructura.

---

``ERR-INF-023`` – Unauthorized (401)

**Descripción**: Autenticación fallida al consumir un servicio externo.

**Síntomas**: 

- HTTP 401
- Token inválido o expirado.

**Identificación**: Verificación de encabezados o expiración.

**Origen**: Infraestructura.

---

``ERR-INF-024`` – Forbidden (403)

**Descripción**: Acceso denegado incluso con autenticación válida.

**Síntomas**: 

- HTTP 403
- Falta de permisos.

**Identificación**: Verificar scope o permisos del token.

**Origen**: Infraestructura.

---

``ERR-INF-025`` – Service Unavailable (503)

**Descripción**: Servicio remoto no está disponible temporalmente.

**Síntomas**: 

- HTTP 503 
- Service unavailable.

**Identificación**: Consultar estado del proveedor externo.

**Origen**: Infraestructura.

---

``ERR-INF-026`` – Malformed Response

**Descripción**: El servicio responde con un body mal formado.

**Síntomas**: 

- Unexpected token
- JSON parse error.

**Identificación**: Logs del cliente, traza de parsing.

**Origen**: Infraestructura.

---

``ERR-INF-027`` – Too Many Open Files

**Descripción**: El proceso ha alcanzado el límite de archivos abiertos.

**Síntomas**: 

- EMFILE
- Too many open files.

**Identificación**: Verificación de ulimit, conteo de FDs abiertos.

**Origen**: Infraestructura.

---

``ERR-INF-028`` – Out of Memory (OOM)

**Descripción**: El proceso fue terminado o falló por falta de memoria.

**Síntomas**: 

- Killed
- OOM killer
- Cannot allocate memory.

**Identificación**: Logs del kernel, métricas de RAM.

**Origen**: Infraestructura.

---

``ERR-INF-029`` – Insufficient Privileges

**Descripción**: El proceso intentó realizar una acción sin privilegios suficientes.

**Síntomas**: 

- Operation not permitted
- Permission denied.

**Identificación**: Verificación de usuario/rol del proceso.

**Origen**: Infraestructura.

---

¿Cómo identificar errores no contemplados aquí?
Si enfrentas un error no listado, verifica estas condiciones para determinar si pertenece a infraestructura:

- ¿Depende de un sistema externo o recurso técnico?
- ¿Se manifiesta sin lógica de negocio involucrada?
- ¿Se relaciona con conectividad, almacenamiento, ejecución o entorno?
- ¿Requiere intervención operativa, no lógica, para solucionarse?

Si la respuesta es sí a al menos dos, es un error de infraestructura.

---

## Errores de dominio

---

``ERR-DOM-001`` – Violación de Invariante de Entidad

**Descripción**: Una entidad del dominio está en un estado inválido porque una de sus invariantes fue violada. Las invariantes son reglas que siempre deben cumplirse para que la entidad sea válida (por ejemplo, un pedido debe tener al menos un ítem).

**Ejemplos**:
Un objeto CuentaBancaria permite saldo negativo, aunque se definió como no sobregirable.

**Síntomas**:

- El objeto parece válido, pero genera inconsistencias al usarse.
- Pruebas de unidad fallan por valores que deberían ser inválidos.

**Cómo identificarlo**: Revisa reglas como “un cliente debe tener al menos un contrato activo”, o “la suma de partes debe ser igual al total”. Si puedes construir un objeto que viole esto, hay una falla.

**Origen**: Dominio.

---

``ERR-DOM-002`` – Regla de Negocio Violada
**Descripción**: Una operación del dominio ejecuta una acción que no está permitida por las reglas del negocio, incluso si es técnicamente válida. No es que los datos sean inválidos, sino que la transición de estado no está permitida.

**Ejemplo**:
Intentar aprobar una orden que ya fue cancelada.

**Síntomas**:
- Cambios de estado inconsistentes.
- Conflictos entre estados de distintos objetos relacionados.

**Cómo identificarlo**:
Si puedes ejecutar una acción que tu equipo de negocio o cliente diría “eso no se debería poder hacer”, estás frente a esta clase de error.

**Origen**: Dominio.

---

``ERR-DOM-003`` – Valor de Objeto Inválido

**Descripción**: Un objeto de valor contiene un dato que no respeta su contrato semántico. Los objetos de valor deben garantizar su validez desde el momento en que son creados.

**Ejemplo**:
Un Email con formato inválido, o una Moneda con código no estandarizado.

**Síntomas**:

- Fallos en operaciones que dependen del valor (e.g., envío de correo).
- Problemas de interoperabilidad.

**Cómo identificarlo**:
Revisa si puedes construir un objeto de valor con información incorrecta sin que lance error. Si es así, está mal diseñado.

**Origen**: Dominio.

---

``ERR-DOM-004`` – Estado Transitorio o Incompleto

**Descripción**: El dominio permite mantener objetos en estados intermedios que no deberían ser accesibles o que requieren pasos adicionales para ser válidos.

**Ejemplo**:
Un ProcesoDeRegistro que puede persistirse aunque falte la verificación de correo.

**Síntomas**:

- Objetos con campos nulos que deberían ser obligatorios.
- Acciones disponibles que no deberían serlo hasta completar un flujo.

**Cómo identificarlo**:
Busca estructuras de datos parcialmente construidas o estados de transición no controlados.

**Origen**: Dominio.

---

``ERR-DOM-005`` – Conflicto de Identidad de Entidad

**Descripción**: Se crea o utiliza una entidad con un identificador que entra en conflicto con otra entidad existente, provocando ambigüedad en el dominio.

**Ejemplo**:
Dos objetos Usuario con el mismo correo o ID en un sistema donde esto no es permitido.

**Síntomas**:

- Registros duplicados.
- Acciones en una entidad afectan accidentalmente otra.

**Cómo identificarlo**:
El dominio debe garantizar unicidad donde se espera. Si eso falla, es un problema de identidad.

**Origen**: Dominio.

---

``ERR-DOM-006`` – Agregados Mal Delimitados

**Descripción**: Se permite manipular directamente entidades internas de un agregado desde fuera, violando el principio de consistencia transaccional.

**Ejemplo**:
Modificar un Producto dentro de un Pedido sin pasar por el agregado Pedido.

**Síntomas**:

- Modificaciones inconsistentes.
- Estados imposibles de alcanzar desde la raíz del agregado.

**Cómo identificarlo**:
Si puedes romper las reglas del modelo sin pasar por el agregado raíz, estás frente a este error.

**Origen**: Dominio.

---

``ERR-DOM-007`` – Referencia Rota o No Existente

**Descripción**: Una entidad o valor referencia otra entidad del dominio que no existe o no es válida en ese contexto.

**Ejemplo**:
Asignar un Cliente que ya fue eliminado a una nueva Orden.

**Síntomas**:

- Excepciones al cargar datos.
- Inconsistencias en el flujo de negocio.

**Cómo identificarlo**:
Rastrea relaciones internas y asegúrate de que todas las referencias sean válidas y coherentes con las reglas del dominio.

**Origen**: Dominio.

---


``ERR-DOM-008`` – Uso de Datos Externos que Rompen el Modelo

**Descripción**: Datos provenientes de sistemas externos son utilizados sin pasar por las validaciones o normalizaciones del dominio, rompiendo su coherencia interna.

**Ejemplo**:
Inyectar un JSON de un sistema externo como entidad del dominio sin validarlo.

**Síntomas**:

- Inconsistencias que solo ocurren con datos externos.
- Valores fuera de rangos esperados.

**Cómo identificarlo**:
Verifica si los datos externos pueden construirse en entidades sin validación explícita.

**Origen**: Dominio.

--- 

¿Cómo identificar errores de dominio que no están listados?
Para determinar si un error pertenece a la capa de dominio, evalúa:

- ¿El error representa una violación de una regla del negocio?
- ¿La estructura del objeto o su uso rompe las invariantes del modelo?
- ¿El sistema técnico funciona bien, pero el modelo lógico se comporta de forma incorrecta?
- ¿Un experto del negocio encontraría inaceptable el estado resultante?

Si respondes sí a una o más, el error pertenece claramente a la capa de dominio.

--