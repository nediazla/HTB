# Preparation Stage (Part 1)

En el `preparation` etapa, tenemos dos objetivos separados. El primero es el establecimiento de la capacidad de manejo de incidentes dentro de la organización. El segundo es la capacidad de protegerse y prevenir incidentes de seguridad de TI mediante la implementación de medidas de protección adecuadas. Dichas medidas incluyen refuerzo de terminales y servidores, niveles de directorio activo, autenticación multifactor, administración de acceso privilegiado, etc. Si bien la protección contra incidentes no es responsabilidad del equipo de manejo de incidentes, esta actividad es fundamental para el éxito general de ese equipo.

---

# **Requisitos previos de preparación**

Durante la preparación, debemos asegurarnos de tener:

- Miembros capacitados del equipo de manejo de incidentes (los miembros del equipo de manejo de incidentes se pueden subcontratar, pero de todos modos es necesario tener una capacidad básica y una comprensión del manejo de incidentes internamente)
- Fuerza laboral capacitada (en la medida de lo posible, a través de actividades de concientización sobre seguridad u otros medios de capacitación)
- Políticas y documentación claras
- Herramientas (software y hardware)

---

# **Políticas y documentación claras**

Algunas de las políticas y documentación escritas deben contener una versión actualizada de la siguiente información:

- Información de contacto y funciones de los miembros del equipo de gestión de incidentes.
- Información de contacto del departamento legal y de cumplimiento, el equipo de administración, el soporte de TI, el departamento de comunicaciones y relaciones con los medios, las fuerzas del orden, los proveedores de servicios de Internet, la administración de instalaciones y el equipo externo de respuesta a incidentes.
- Política, plan y procedimientos de respuesta a incidentes
- Política y procedimientos para compartir información sobre incidentes
- Líneas base de sistemas y redes, fuera de una imagen dorada y un entorno de estado limpio
- Diagramas de red
- Base de datos de gestión de activos de toda la organización
- Cuentas de usuario con privilegios excesivos que el equipo puede utilizar bajo demanda cuando sea necesario (también para sistemas críticos para el negocio, que se manejan con las habilidades necesarias para administrar ese sistema específico). Estas cuentas de usuario normalmente se habilitan cuando se confirma un incidente durante la investigación inicial y luego se deshabilitan una vez finalizada. También se realiza un restablecimiento de contraseña obligatorio al deshabilitar a los usuarios.
- Capacidad de adquirir hardware, software o un recurso externo sin un proceso de adquisición completo (compra urgente de hasta un monto determinado). Lo último que necesita durante un incidente es esperar semanas para la aprobación de una herramienta de $500.
- Hojas de referencia forenses/de investigación

Algunos de los casos no graves pueden manejarse con relativa rapidez y sin demasiadas fricciones dentro o fuera de la organización. Otros casos pueden requerir notificación a las autoridades y comunicación externa a clientes y proveedores externos, especialmente en casos de inquietudes legales que surjan del incidente. Por ejemplo, una violación de datos que involucre datos de clientes debe informarse a las autoridades policiales dentro de un cierto umbral de tiempo de acuerdo con el RGPD. Puede haber muchos requisitos de cumplimiento dependiendo de la ubicación y/o sucursales donde ocurrió el incidente, por lo que la mejor manera de comprenderlos es discutirlos con sus equipos legales y de cumplimiento por incidente (o de manera proactiva).

Si bien contar con documentación es vital, también es importante documentar el incidente mientras se investiga. Por lo tanto, durante esta etapa también deberá establecer una capacidad de presentación de informes eficaz. Los incidentes pueden ser extremadamente estresantes y es fácil olvidar esta parte a medida que se desarrolla el incidente, especialmente cuando estás concentrado y vas extremadamente rápido para resolverlo lo antes posible. Trate de mantener la calma, tome notas y asegúrese de que estas notas contengan marcas de tiempo, la actividad realizada, el resultado de la misma y quién la realizó. En general, se deben buscar respuestas sobre quién, qué, cuándo, dónde, por qué y cómo.

---

# **Herramientas (software y hardware)**

En el futuro, también debemos asegurarnos de contar con las herramientas adecuadas para realizar el trabajo. Estos incluyen, entre otros:

- Una computadora portátil adicional o una estación de trabajo forense para cada miembro del equipo de manejo de incidentes para preservar imágenes de disco y archivos de registro, realizar análisis de datos e investigar sin restricciones (sabemos que aquí se probará el malware, por lo que se deben desactivar herramientas como el antivirus). Estos dispositivos deben manipularse de manera adecuada y no de una manera que introduzca riesgos para la organización.
- Herramientas digitales de adquisición y análisis de imágenes forenses.
- Herramientas de captura y análisis de memoria.
- Captura y análisis de respuesta en vivo
- Herramientas de análisis de registros
- Herramientas de captura y análisis de red.
- Cables y conmutadores de red.
- Bloqueadores de escritura
- Discos duros para imágenes forenses
- Cables de alimentación
- Destornilladores, pinzas y otras herramientas relevantes para reparar o desmontar dispositivos de hardware si es necesario
- Creador del indicador de compromiso (IOC) y capacidad de buscar IOC en toda la organización
- Formularios de cadena de custodia
- Software de cifrado
- Sistema de seguimiento de billetes
- Instalación segura para almacenamiento e investigación.
- Sistema de gestión de incidencias independiente de la infraestructura de su organización

Muchas de las herramientas mencionadas anteriormente formarán parte de lo que se conoce como `jump bag` - siempre listo con las herramientas necesarias para ser recogido y salido de inmediato. Sin esta bolsa preparada, reunir todas las herramientas necesarias sobre la marcha puede llevar días o semanas antes de que esté listo para responder.

Finalmente, queremos resaltar la importancia de tener su sistema de documentación completamente independiente de la infraestructura de su organización y debidamente asegurado. Suponga desde el principio que todo su dominio está comprometido y que todos los sistemas pueden dejar de estar disponibles. De manera similar, las comunicaciones sobre un incidente deben realizarse a través de canales que no formen parte de los sistemas de la organización; Supongamos que los adversarios tienen control sobre todo y pueden leer canales de comunicación como el correo electrónico.