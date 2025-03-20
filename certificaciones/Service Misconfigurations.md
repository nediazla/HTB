# Service Misconfigurations

Las configuraciones erróneas generalmente ocurren cuando un administrador del sistema, soporte técnico o desarrollador no configura correctamente el marco de seguridad de una aplicación, sitio web, escritorio o servidor que conduce a rutas abiertas peligrosas para usuarios no autorizados. Exploremos algunas de las configuraciones erróneas más típicas de los servicios comunes.

---

# **Autenticación**

En años anteriores (aunque todavía vemos esto a veces durante las evaluaciones), estaba muy extendido que los servicios fueran credenciales predeterminados (nombre de usuario y contraseña). Esto presenta un problema de seguridad porque muchos administradores dejan las credenciales predeterminadas sin cambios. Hoy en día, la mayoría del software les pide a los usuarios que configure credenciales tras la instalación, que es mejor que las credenciales predeterminadas. Sin embargo, tenga en cuenta que aún encontraremos a los proveedores que usan credenciales predeterminadas, especialmente en aplicaciones anteriores.

Incluso cuando el Servicio no tiene un conjunto de credenciales predeterminadas, un administrador puede usar contraseñas débiles o ninguna contraseña al configurar los servicios con la idea de que cambiará la contraseña una vez que el servicio esté configurado y en funcionamiento.

Como administradores, necesitamos definir las políticas de contraseña que se aplican al software probado o instalado en nuestro entorno. Se debe requerir que los administradores cumplan con una complejidad mínima de contraseña para evitar combinaciones de usuarios y contraseñas, tales como:

Configuraciones erróneas del servicio

```
admin:admin
admin:password
admin:<blank>
root:12345678
administrator:Password

```

Una vez que tomamos el banner de servicio, el siguiente paso debe ser identificar posibles credenciales predeterminadas. Si no hay credenciales predeterminadas, podemos probar las combinaciones débiles de nombre de usuario y contraseña enumeradas anteriormente.

### **Autenticación anónima**

Otra configuración errónea que puede existir en servicios comunes es la autenticación anónima. El servicio se puede configurar para permitir la autenticación anónima, permitiendo a cualquier persona con conectividad de red al servicio sin que se le solicite la autenticación.

### **Derechos de acceso mal configurados**

Imaginemos que recuperamos las credenciales para un usuario cuyo rol es cargar archivos en el servidor FTP, pero se le dio el derecho de leer cada documento FTP. La posibilidad es interminable, dependiendo de lo que esté dentro del servidor FTP. Podemos encontrar archivos con información de configuración para otros servicios, credenciales de texto sin formato, nombres de usuario, información patentada e información de identificación personal (PII).

Los derechos de acceso mal configurados son cuando las cuentas de los usuarios tienen permisos incorrectos. El mayor problema podría ser dar a las personas más bajas el acceso de la cadena de comando a la información privada que solo los gerentes o administradores deberían tener.

Los administradores deben planificar su estrategia de derechos de acceso, y hay algunas alternativas como[Control de acceso basado en roles (RBAC)](https://en.wikipedia.org/wiki/Role-based_access_control), [Listas de control de acceso (ACL)](https://en.wikipedia.org/wiki/Access-control_list). Si queremos pros y contras más detallados de cada método, podemos leer[Elegir la mejor estrategia de control de acceso](https://authress.io/knowledge-base/role-based-access-control-rbac)por Warren Parad de Authress.

---

# **Valores predeterminados innecesarios**

La configuración inicial de dispositivos y software puede incluir, entre otros, configuraciones, características, archivos y credenciales. Esos valores predeterminados generalmente están dirigidos a la usabilidad en lugar de a la seguridad. Dejarlo por defecto no es una buena práctica de seguridad para un entorno de producción. Los valores predeterminados innecesarios son aquellas configuraciones que debemos cambiar para asegurar un sistema reduciendo su superficie de ataque.

También podríamos entregar la información personal de nuestra empresa en un plato de plata si tomamos el camino fácil y aceptamos la configuración predeterminada mientras configuramos software o un dispositivo por primera vez. En realidad, los atacantes pueden obtener credenciales de acceso para equipos específicos o abusar de un entorno débil mediante la realización de una breve búsqueda en Internet.

[Configuración errónea de seguridad](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)son parte del[Lista Top 10 de OWASP](https://owasp.org/Top10/). Echemos un vistazo a los relacionados con los valores predeterminados:

- Las características innecesarias están habilitadas o instaladas (por ejemplo, puertos innecesarios, servicios, páginas, cuentas o privilegios).
- Las cuentas predeterminadas y sus contraseñas aún están habilitadas y sin cambios.
- El manejo de errores revela trazas de pila u otros mensajes de error demasiado informativos a los usuarios.
- Para los sistemas actualizados, las últimas funciones de seguridad están deshabilitadas o no configuradas de forma segura.

---

# **Prevención de la configuración errónea**

Una vez que hemos descubierto nuestro entorno, la estrategia más directa para controlar el riesgo es bloquear la infraestructura más crítica y solo permitir el comportamiento deseado. Cualquier comunicación que no sea requerida por el programa debe deshabilitarse. Esto puede incluir cosas como:

- Las interfaces de administración deben deshabilitarse.
- La depuración está apagada.
- Deshabilite el uso de nombres de usuario y contraseñas predeterminados.
- Configure el servidor para evitar el acceso no autorizado, el listado de directorio y otros problemas.
- Ejecute escaneos y auditorías regularmente para ayudar a descubrir futuras configuraciones erróneas o soluciones faltantes.

El OWASP Top 10 proporciona una sección sobre cómo asegurar los procesos de instalación:

- Un proceso de endurecimiento repetible hace que sea rápido y fácil de implementar otro entorno que esté bloqueado adecuadamente. El desarrollo, el control de calidad y los entornos de producción deben configurarse de manera idéntica, con diferentes credenciales utilizadas en cada entorno. Además, este proceso debe automatizarse para minimizar el esfuerzo requerido para configurar un nuevo entorno seguro.
- Una plataforma mínima sin características, componentes, documentación y muestras innecesarias. Eliminar o no instalar características y marcos no utilizados.
- Una tarea para revisar y actualizar las configuraciones apropiadas para todas las notas de seguridad, actualizaciones y parches como parte del proceso de administración de parches (consulte los componentes A06: 2021-vulnerables y obsoletos). Revise los permisos de almacenamiento en la nube (por ejemplo, permisos de cubo S3).
- Una arquitectura de aplicación segmentada proporciona una separación efectiva y segura entre componentes o inquilinos, con segmentación, contenedores o grupos de seguridad en la nube (ACL).
- Enviar directivas de seguridad a clientes, por ejemplo, encabezados de seguridad.
- Un proceso automatizado para verificar la efectividad de las configuraciones y configuraciones en todos los entornos.