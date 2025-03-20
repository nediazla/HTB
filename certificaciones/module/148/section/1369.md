# Containment, Eradication, & Recovery Stage

Cuando se completa la investigación y hemos entendido el tipo de incidente y el impacto en el negocio (según todas las pistas recopiladas y la información recopilada en el cronograma), es hora de entrar en la etapa de contención para evitar que el incidente cause más. daño.

---

# **Contención**

En esta etapa, tomamos medidas para evitar la propagación del incidente. Dividimos las acciones en `short-term containment` y `long-term containment`. Es importante que las acciones de contención se coordinen y ejecuten en todos los sistemas simultáneamente. De lo contrario, corremos el riesgo de notificar a los atacantes que los estamos persiguiendo, en cuyo caso podrían cambiar sus técnicas y herramientas para persistir en el entorno.

En la contención a corto plazo, las acciones tomadas dejan una huella mínima en los sistemas en los que ocurren. Algunas de estas acciones pueden incluir colocar un sistema en una VLAN separada/aislada, desconectar el cable de red del sistema o modificar el nombre DNS C2 del atacante a un sistema bajo nuestro control o a uno inexistente. Las acciones aquí contienen el daño y brindan tiempo para desarrollar una estrategia de remediación más concreta. Además, dado que mantenemos los sistemas inalterados (tanto como sea posible), tenemos la oportunidad de tomar imágenes forenses y preservar evidencia si esto no se hizo ya durante la investigación (esto también se conoce como `backup`subetapa de la etapa de contención). Si una acción de contención a corto plazo requiere cerrar un sistema, debemos asegurarnos de que esto se comunique a la empresa y que se otorguen los permisos adecuados.

En las acciones de contención a largo plazo, nos centramos en acciones y cambios persistentes. Estos pueden incluir cambiar las contraseñas de los usuarios, aplicar reglas de firewall, insertar un sistema de detección de intrusiones en el host, aplicar un parche del sistema y apagar los sistemas. Mientras realizamos estas actividades, debemos mantener actualizados el negocio y las partes interesadas relevantes. Tenga en cuenta que el hecho de que un sistema esté parcheado no significa que el incidente haya terminado. Aún quedan pendientes las actividades de erradicación, recuperación y post-incidente.

---

# **Erradicación**

Una vez contenido el incidente, es necesaria la erradicación para eliminar tanto la causa raíz del incidente como lo que queda de él para garantizar que el adversario esté fuera de los sistemas y la red. Algunas de las actividades de esta etapa incluyen eliminar el malware detectado de los sistemas, reconstruir algunos sistemas y restaurar otros desde la copia de seguridad. Durante la etapa de erradicación, podremos ampliar las actividades de contención realizadas anteriormente mediante la aplicación de parches adicionales, que no fueron necesarios de inmediato. A menudo se realizan actividades adicionales de fortalecimiento del sistema durante la etapa de erradicación (no sólo en el sistema afectado sino en algunos casos en toda la red).

---

# **Recuperación**

En la etapa de recuperación, devolvemos los sistemas a su funcionamiento normal. Por supuesto, la empresa necesita verificar que un sistema esté funcionando como se espera y que contenga todos los datos necesarios. Cuando todo está verificado, estos sistemas se llevan al entorno de producción. Todos los sistemas restaurados estarán sujetos a un intenso registro y monitoreo después de un incidente, ya que los sistemas comprometidos tienden a ser objetivos nuevamente si el adversario recupera el acceso al entorno en un corto período de tiempo. Los eventos sospechosos típicos que se deben monitorear son:

- Inicios de sesión inusuales (por ejemplo, cuentas de usuario o de servicio que nunca antes han iniciado sesión)
- Procesos inusuales
- Cambios en el registro en ubicaciones que suelen ser modificadas por malware

La etapa de recuperación en algunos incidentes grandes puede llevar meses, ya que a menudo se aborda en fases. Durante las primeras fases, la atención se centra en aumentar la seguridad general para evitar incidentes futuros mediante ganancias rápidas y la eliminación de los frutos más fáciles. Las últimas fases se centran en cambios permanentes y a largo plazo para mantener la organización lo más segura posible.