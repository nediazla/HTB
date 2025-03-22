# Actualizaciones de trabajo de metasploit - agosto de 2020

La actualización de MSF6 hará que todas las sesiones de carga útil anteriores sean inutilizables si se establecían utilizando MSF5. Además, las cargas útiles generadas con MSF5 no funcionarán con mecanismos de comunicación MSF6. Hemos resumido los cambios y adiciones que las actualizaciones MSFConsole de agosto de 2020 trajeron a continuación.

---

# **Características de generación**

- Cifrado de fin a extremo en sesiones de meterpreter para las cinco implementaciones (Windows, Python, Java, Mettle y PHP)
- Soporte del cliente SMBV3 para habilitar más flujos de trabajo de explotación modernos
- Nuevo rutina de generación de carga útil polimórfica para Windows Shellcode que mejora las capacidades evasivas contra los productos comunes antivirus y del sistema de detección de intrusos (IDS)

---

# **Cifrado expandido**

- Aumento de la complejidad para la creación de detecciones basadas en la firma para ciertas operaciones de red y los principales binarios de carga útil de MetaSploit
- Todas las cargas útiles de Meterpreter utilizarán el cifrado AES durante la comunicación entre el atacante y el sistema objetivo
- La integración de cifrado SMBV3 aumentará la complejidad para las detecciones basadas en la firma utilizadas para identificar las operaciones clave realizadas sobre SMB

---

# **Artefactos de carga útil más limpia**

- Las DLL utilizadas por el meterpreter de Windows ahora resuelven las funciones necesarias por ordinal en lugar de nombre
- La exportación estándar reflejador de reflejos utilizados por DLLS reflexivamente cargables ya no está presente en los binarios de carga útil como datos de texto
- Los comandos que Meterpreter expone al marco ahora están codificados como enteros en lugar de cadenas

---

# **Complementos**

La antigua extensión de Mimikatz Meterpreter fue eliminada a favor de su sucesor, Kiwi. Por lo tanto, los intentos de cargar Mimikatz cargarán Kiwi en el futuro previsible.

---

# **Cargas útiles**

Reemplazó la rutina de generación estática de shellcode con una rutina de aleatorización que agrega propiedades polimórficas a este trozo crítico al barajar las instrucciones cada vez. Para leer más sobre estos cambios y ver el cambio de cambio completo, por favor[Sigue este enlace](https://blog.rapid7.com/2020/08/06/metasploit-6-now-under-active-development/).

---

# **Pensamientos de cierre**

Como hemos visto en este módulo, MetaSploit es un marco poderoso. Aunque a menudo se usa mal y mal etiquetado, puede ser una parte importante de nuestro arsenal de prueba de penetración cuando se usa correctamente. Es altamente extensible excelente para rastrear datos durante una evaluación, y excelente para la explotación posterior y facilitar la pivotación. Vale la pena experimentar con todas las características que MetaSploit tiene para ofrecer; Puede encontrar una manera de que se ajuste bien a su flujo de trabajo. Si prefieres evitarlo, ¡eso también está bien! Hay muchas herramientas por ahí, y debemos trabajar con lo que nos sentimos más cómodos. Para obtener más práctica con esta herramienta, consulte los cuadros HTB etiquetados al final de este módulo, o intente cualquier cuadro o objetivo del módulo de la Academia usando MetaSploit. También puede practicar con él (especialmente su poder para pivotar) en el laboratorio Dante Pro.

[Anterior](https://academy.hackthebox.com/module/39/section/416)