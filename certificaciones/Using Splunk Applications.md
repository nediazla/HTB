# Using Splunk Applications

# **Aplicaciones Splunk**

Las aplicaciones o apps de Splunk son paquetes que agregamos a nuestras implementaciones de Splunk Enterprise o Splunk Cloud para ampliar las capacidades y administrar tipos específicos de datos operativos. Cada aplicación está diseñada para manejar datos de tecnologías o casos de uso específicos, actuando efectivamente como un paquete de conocimiento prediseñado para esos datos. Las aplicaciones pueden proporcionar capacidades que van desde entradas de datos personalizadas, visualizaciones personalizadas, paneles, alertas, informes y más.

Las aplicaciones Splunk permiten la coexistencia de múltiples espacios de trabajo en una sola instancia de Splunk, atendiendo a diferentes casos de uso y roles de usuario. Estas aplicaciones listas para usar se pueden encontrar en Splunkbase.

Como parte integral de nuestras operaciones de ciberseguridad, las aplicaciones de Splunk diseñadas para fines de gestión de eventos e información de seguridad (SIEM) brindan una variedad de capacidades para mejorar nuestra capacidad de detectar, investigar y responder a amenazas. Están diseñados para ingerir, analizar y visualizar datos relacionados con la seguridad, lo que nos permite detectar amenazas complejas y realizar investigaciones en profundidad.

Al utilizar estas aplicaciones en nuestro entorno Splunk, debemos considerar factores como el volumen de datos, los requisitos de hardware y las licencias. Muchas aplicaciones pueden consumir muchos recursos, por lo que debemos asegurarnos de que nuestra implementación de Splunk tenga el tamaño correcto para manejar la carga de trabajo adicional. Además, también es importante asegurarnos de tener las licencias correctas para cualquier aplicación premium y que seamos conscientes del potencial de un mayor uso de licencias debido a las entradas de datos adicionales.

En este segmento, aprovecharemos la `Sysmon App for Splunk` desarrollado por Mike Haag.

Para descargar, agregar y utilizar esta aplicación, siga los pasos que se detallan a continuación:

1. Regístrese para obtener una cuenta gratuita en [base splunk](https://splunkbase.splunk.com/)
    
    ![](https://academy.hackthebox.com/storage/modules/218/116.png)
    
2. Una vez registrado, inicie sesión en su cuenta
3. Dirígete al [Aplicación Sysmon para Splunk](https://splunkbase.splunk.com/app/3544) página para descargar la aplicación.
    
    ![](https://academy.hackthebox.com/storage/modules/218/117.png)
    
4. Agregue la aplicación de la siguiente manera a su cabezal de búsqueda.
    
    ![](https://academy.hackthebox.com/storage/modules/218/118.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/119.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/120.png)
    
5. Ajustar la aplicación [macro](https://docs.splunk.com/Documentation/Splunk/latest/Knowledge/Definesearchmacros) para que los eventos se carguen de la siguiente manera.
    
    ![](https://academy.hackthebox.com/storage/modules/218/121.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/122.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/123.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/124.png)
    

Accedamos a la aplicación Sysmon para Splunk ubicándola en la columna "Aplicaciones" en la página de inicio de Splunk y dirigiéndonos a

```
File Activity
```

pestaña.

![](https://academy.hackthebox.com/storage/modules/218/125.png)

Ahora especifiquemos "Todo el tiempo" en el selector de tiempo y hagamos clic en "Enviar". Los resultados se generan exitosamente; sin embargo, no aparecen resultados en la sección "Sistemas principales".

![](https://academy.hackthebox.com/storage/modules/218/126.png)

Podemos solucionarlo haciendo clic en "Editar" (esquina superior derecha de la pantalla) y editando la búsqueda.

![](https://academy.hackthebox.com/storage/modules/218/127.png)

Los eventos Sysmon con ID 11 no contienen un campo llamado

```
Computer
```

, pero sí incluyen un campo llamado

```
ComputerName
```

. Arreglemos eso y hagamos clic en "Aplicar"

![](https://academy.hackthebox.com/storage/modules/218/128.png)

Los resultados ahora deberían generarse exitosamente en la sección "Sistemas principales".

![](https://academy.hackthebox.com/storage/modules/218/129.png)

Siéntete libre de explorar y experimentar con esta aplicación de Splunk. Un excelente ejercicio es modificar las búsquedas cuando no se generan resultados por especificar campos inexistentes, continuando hasta obtener los resultados deseados.

---