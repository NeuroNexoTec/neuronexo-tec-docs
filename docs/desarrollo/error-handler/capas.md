# Capas de implementacion

Implementar un sistema robusto y confiable no depende únicamente de escribir funciones que “hagan lo que deben”, sino de anticipar lo que puede salir mal, identificarlo con precisión, gestionarlo con criterio y comunicarlo con claridad. Esta responsabilidad se distribuye a lo largo de distintas capas del sistema, cada una con un rol específico en el tratamiento de los errores.

El estándar de manejo de errores se construye sobre cinco capas fundamentales: infraestructura, dominio, aplicación, presentación y observabilidad. Comprender la función y los escenarios característicos de cada una de ellas es esencial para desarrollar software profesional, mantenible y resiliente. A continuación, se describen estas capas en profundidad, junto con ejemplos claros para facilitar su comprensión.

## Capa de Infraestructura

La capa de infraestructura constituye el cimiento técnico sobre el cual se apoya un sistema. Es la capa encargada de interactuar con el "mundo exterior" al software: bases de datos, redes, archivos, servicios de terceros, almacenamiento en la nube, colas de mensajes, proveedores de autenticación, entre muchos otros recursos. Aunque no contiene lógica de negocio, su correcta operación es esencial para que el sistema funcione.

En el contexto de un estándar de manejo de errores, la capa de infraestructura representa el punto inicial donde los errores técnicos pueden originarse. Por ello, debe estar preparada para identificar, encapsular y propagar de manera estandarizada cualquier error que surja en su interacción con sistemas externos o recursos físicos.

### Características de la Capa de Infraestructura

- **Intermediaria técnica**: No define reglas de negocio, sino que se limita a cumplir requerimientos técnicos solicitados por capas superiores.
- **Volátil por naturaleza**: Depende de elementos externos que pueden fallar (latencia de red, disponibilidad de servicios, errores en discos, etc.).
- **No toma decisiones**: Informa a la capa de aplicación o dominio, pero no define cómo se debe reaccionar ante un error.
- **Abstraída del consumidor**: Los errores que detecta deben ser encapsulados de forma que no expongan detalles irrelevantes a otras capas o al usuario final.

### Escenarios comunes
---

**Errores de Conexión a Base de Datos**

**Escenario**: El sistema intenta conectarse a una base de datos SQL, pero el servidor no responde. Puede ser por red, saturación del servicio o configuración incorrecta.

**Cómo identificarlo**: El sistema lanza una excepción de tiempo de espera o conexión rechazada. El error ocurre incluso antes de ejecutar una consulta.

**Ejemplo práctico**: Al iniciar la aplicación, falla la validación de conexión con el mensaje: “Database unreachable”.

---

***Errores en Lectura/Escritura de Archivos***


**Escenario**: Se intenta guardar un archivo en un sistema de archivos y no hay espacio disponible, o la ruta no existe.

**Cómo identificarlo**: El sistema devuelve un error de tipo "permission denied", “disk full” o “path not found”.

**Ejemplo práctico**: Una función de respaldo intenta copiar un archivo de log y falla porque se cambió el volumen de almacenamiento.

---

**Fallas en Servicios Externos (APIs de terceros)**

**Escenario**: Se hace una llamada a una API externa y esta responde con un código 500, un 429 (too many requests) o directamente no responde.

**Cómo identificarlo**: El sistema recibe errores HTTP, mensajes de “bad gateway” o tiempos de espera.

**Ejemplo práctico**: El sistema intenta validar una tarjeta de crédito con un proveedor de pagos, pero la API responde con "internal server error".

---

**Errores en Colas de Mensajes**

**Escenario**: El sistema intenta publicar o consumir un mensaje de una cola (como RabbitMQ, Kafka o SQS), pero hay problemas de conexión o desincronización.

**Cómo identificarlo**: Se observan errores como “broker not available”, “queue not found” o pérdida de confirmación.

**Ejemplo práctico**: Se intenta publicar una notificación a una cola, pero el mensaje no es aceptado por una política de tamaño máximo superado.

---

**Problemas en Sistemas de Cache (Redis, Memcached)**

**Escenario**: Al consultar el cache para obtener una sesión de usuario, Redis está caído o responde con errores.

**Cómo identificarlo**: El sistema lanza un error por socket cerrado, comando no reconocido, o timeout.

**Ejemplo práctico**: La aplicación pierde la sesión del usuario y lo fuerza a autenticarse nuevamente debido a una falla silenciosa en Redis.

---

**Errores de Autenticación Externa**

**Escenario**: El sistema necesita autenticarse contra un sistema federado (LDAP, OAuth, OpenID Connect), pero las credenciales fallan o el proveedor no está disponible.

**Cómo identificarlo**: El sistema recibe errores de “access denied”, “invalid credentials” o códigos HTTP como 401 o 403.

**Ejemplo práctico**: Un usuario intenta ingresar con autenticación corporativa, pero el servidor LDAP está fuera de servicio.

---

**Errores de Configuración y Variables de Entorno**

**Escenario**: La infraestructura requiere ciertas configuraciones en variables de entorno o archivos de configuración, pero estos están mal definidos.

**Cómo identificarlo**: Al iniciar la aplicación, se detectan errores como "missing configuration" o valores inválidos para parámetros críticos.

**Ejemplo práctico**: La URL de la base de datos contiene un error tipográfico que hace fallar toda la cadena de conexión.

---

### Rol en el manejo de errores

En estos casos, los errores no reflejan una falla lógica del sistema, sino una condición técnica inesperada. La capa de infraestructura debe detectar estas condiciones, encapsular la información técnica relevante, y trasladarla al resto del sistema mediante un mecanismo uniforme. No debe tomar decisiones de negocio, sino informar con precisión que algo técnico falló.

---

2. Capa de Dominio
Descripción
La capa de dominio representa el conocimiento profundo del problema que el sistema busca resolver. Aquí se define lo que es válido o inválido dentro del contexto del negocio, sin importar los aspectos técnicos del sistema.

Escenarios comunes
Se intenta aprobar un préstamo sin que el cliente tenga los requisitos mínimos establecidos por la política de crédito.

Se crea una orden de compra con un producto que ya no está disponible en el catálogo.

Se intenta registrar a un usuario con una dirección de correo que ya está asociada a otra cuenta.

Rol en el manejo de errores
Los errores de esta capa son violaciones de reglas del negocio. No ocurren por fallos técnicos, sino porque una operación solicitada no cumple con las condiciones lógicas necesarias para ejecutarse. Aquí es fundamental que los errores sean explícitos, entendibles y categorizados según el tipo de infracción, para que el resto del sistema pueda actuar en consecuencia (por ejemplo, notificando al usuario o impidiendo una acción posterior).

3. Capa de Aplicación
Descripción
La capa de aplicación orquesta los diferentes casos de uso del sistema. Actúa como un puente entre los requerimientos externos (como una solicitud del usuario) y las reglas internas del negocio. También se encarga de coordinar interacciones entre distintas partes del sistema.

Escenarios comunes
Una solicitud de registro llega al sistema: se validan los datos, se consulta la base de datos, se aplican las reglas de negocio y finalmente se registra al usuario.

Se ejecuta un proceso de facturación automática que implica recuperar datos, calcular montos, y enviar los comprobantes por correo electrónico.

Se programa una tarea recurrente que extrae información de distintas fuentes, la consolida y la archiva.

Rol en el manejo de errores
En esta capa, el sistema debe gestionar los errores detectados por otras capas. Su responsabilidad es decidir qué hacer: repetir un intento, ignorar la operación, emitir una alerta, o registrar una acción de compensación. La capa de aplicación no genera errores nuevos, sino que toma decisiones con base en los errores ya detectados.

Por ejemplo, si un intento de guardar datos falla por una interrupción temporal, la capa de aplicación puede reintentar la operación automáticamente. Si un error de dominio señala una violación de política de negocio, esta capa puede abortar el flujo actual y retornar una respuesta clara.

4. Capa de Presentación
Descripción
La capa de presentación es la interfaz que permite al usuario (humano o sistema externo) interactuar con el software. Puede adoptar diversas formas: una aplicación web, una API, una aplicación móvil, un asistente virtual o un panel administrativo.

Escenarios comunes
Un usuario intenta registrarse desde una aplicación móvil.

Un sistema externo hace una solicitud HTTP para consultar el estado de un pedido.

Un operador utiliza una interfaz administrativa para actualizar los datos de un cliente.

Rol en el manejo de errores
Esta capa tiene como tarea principal traducir los errores internos en respuestas adecuadas para el consumidor del sistema. Debe comunicar lo necesario y ocultar lo irrelevante. Un error técnico o del dominio debe convertirse aquí en un mensaje claro, comprensible y útil.

Por ejemplo:

Si un dato está mal ingresado, la presentación debe señalarlo claramente sin exponer cómo se validó.

Si ocurre un error interno, el sistema puede informar que hubo un problema temporal, sin detallar aspectos técnicos como fallas de red o errores en bases de datos.

Además, la presentación debe categorizar los errores correctamente para que los consumidores del sistema puedan reaccionar de forma automática si es necesario.

5. Capa de Observabilidad
Descripción
La capa de observabilidad es transversal y se ocupa de monitorear el comportamiento del sistema, recolectar información y detectar patrones. Es esencial para la estabilidad y evolución del software, aunque no participe directamente en la ejecución de flujos de negocio.

Escenarios comunes
Un error repetido en la capa de infraestructura sugiere que un proveedor externo no está respondiendo correctamente.

Varias fallas de validación de datos indican que el formulario de registro es confuso para los usuarios.

Un número inusual de errores internos en horas pico sugiere problemas de escalabilidad.

Rol en el manejo de errores
En esta capa se registran y analizan los errores, no se resuelven ni se presentan al usuario. Su propósito es permitir a los equipos técnicos identificar causas raíz, correlacionar eventos y tomar decisiones preventivas. Es el punto de conexión entre los errores en tiempo de ejecución y las acciones correctivas o evolutivas que se tomarán fuera del sistema.