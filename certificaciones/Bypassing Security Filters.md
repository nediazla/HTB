# Bypassing Security Filters

El otro tipo y más común de vulnerabilidad de manipulación de verbos HTTP es causada por`Insecure Coding`Los errores cometidos durante el desarrollo de la aplicación web, que conducen a la aplicación web que no cubren todos los métodos HTTP en ciertas funcionalidades. Esto se encuentra comúnmente en filtros de seguridad que detectan solicitudes maliciosas. Por ejemplo, si se estaba utilizando un filtro de seguridad para detectar vulnerabilidades de inyección y solo verificaba las inyecciones en`POST`Parámetros (por ejemplo`$_POST['parameter']`), es posible evitarlo simplemente cambiando el método de solicitud a`GET`.

---

# **Identificar**

En el`File Manager`Aplicación web, si intentamos crear un nuevo nombre de archivo con caracteres especiales en su nombre (por ejemplo,`test;`), recibimos el siguiente mensaje:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_malicious_request.jpg)

Este mensaje muestra que la aplicación web utiliza ciertos filtros en el back-end para identificar intentos de inyección y luego bloquea cualquier solicitud maliciosa. No importa lo que intentemos, la aplicación web bloquea correctamente nuestras solicitudes y está asegurada contra los intentos de inyección. Sin embargo, podemos probar un ataque de manipulación de verbos HTTP para ver si podemos evitar el filtro de seguridad por completo.

---

# **Explotar**

Para tratar de explotar esta vulnerabilidad, interceptemos la solicitud en Burp Suite (BURP) y luego use

```
Change Request Method
```

Para cambiarlo a otro método:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_GET_request.jpg)

Esta vez, no obtuvimos el`Malicious Request Denied!`mensaje, y nuestro archivo se creó con éxito:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_injected_request.jpg)

Para confirmar si pasamos por alto el filtro de seguridad, debemos intentar explotar la vulnerabilidad que está protegiendo el filtro: una vulnerabilidad de inyección de comando, en este caso. Por lo tanto, podemos inyectar un comando que crea dos archivos y luego verificar si ambos archivos fueron creados. Para hacerlo, usaremos el siguiente nombre del archivo en nuestro ataque (`file1; touch file2;`):

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_filter_bypass.jpg)

Luego, podemos cambiar una vez más el método de solicitud a un

```
GET
```

pedido:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_filter_bypass_request.jpg)

Una vez que enviamos nuestra solicitud, vemos que esta vez ambos`file1`y`file2`fueron creados:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_after_filter_bypass.jpg)

Esto muestra que omitimos con éxito el filtro a través de una vulnerabilidad de manipulación de verbos HTTP y una inyección de comando. Sin la vulnerabilidad de manipulación de verbos HTTP, la aplicación web puede haber sido segura contra ataques de inyección de comandos, y esta vulnerabilidad nos permitió evitar los filtros en su lugar por completo.