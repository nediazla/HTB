# Web Fuzzing

Comenzaremos aprendiendo los conceptos básicos de usar`ffuf`a sitios web de Fuzz para directorios. Ejecutamos el ejercicio en la pregunta a continuación y visitamos la URL que nos da, y vemos el siguiente sitio web:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_main_site.jpg)

El sitio web no tiene enlaces a nada más, ni nos da ninguna información que pueda llevarnos a más páginas. Entonces, parece que nuestra única opción es '`fuzz`'El sitio web.

---

# **Peludo**

El término`fuzzing`se refiere a una técnica de prueba que envía varios tipos de entrada del usuario a una determinada interfaz para estudiar cómo reaccionaría. Si estuviéramos luchando por las vulnerabilidades de inyección SQL, enviaríamos caracteres especiales aleatorios y ver cómo reaccionaría el servidor. Si estuviéramos luchando por un desbordamiento del amortiguador, enviaríamos cuerdas largas e incrementando su longitud para ver si el binario se rompería y cuándo.

Por lo general, utilizamos listas de palabras predefinidas de términos comúnmente utilizados para cada tipo de prueba para la lucha web para ver si el servidor web las aceptaría. Esto se hace porque los servidores web generalmente no proporcionan un directorio de todos los enlaces y dominios disponibles (a menos que se configuren terriblemente), por lo que tendríamos que verificar varios enlaces y ver cuáles devuelven las páginas de devolución. Por ejemplo, si visitamos[https://www.hackthebox.eu/doesnotexist](https://www.hackthebox.eu/doesnotexist), obtendríamos un código HTTP`404 Page Not Found`, y vea la página a continuación:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_HTB_404.jpg)

Sin embargo, si visitamos una página que existe, como`/login`, obtendríamos la página de inicio de sesión y obtendríamos un código HTTP`200 OK`, y vea la página a continuación:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_HTB_login.jpg)

Esta es la idea básica detrás de la lucha web para páginas y directorios. Aún así, no podemos hacer esto manualmente, ya que llevará una eternidad. Es por eso que tenemos herramientas que lo hacen de manera automática, eficiente y muy rápida. Dichas herramientas envían cientos de solicitudes cada segundo, estudian el código HTTP de respuesta y determine si la página existe o no. Por lo tanto, podemos determinar rápidamente qué páginas existen y luego examinarlas manualmente para ver su contenido.

---

# **Listas de palabras**

Para determinar qué páginas existen, debemos tener una lista de palabras que contenga palabras de uso común para directorios y páginas web, muy similar a un`Password Dictionary Attack`, que discutiremos más adelante en el módulo. Aunque esto no revelará todas las páginas en un sitio web específico, ya que algunas páginas tienen nombres al azar o usan nombres únicos, en general, esto devuelve la mayoría de las páginas, alcanzando una tasa de éxito del 90% en algunos sitios web.

No tendremos que reinventar la rueda creando manualmente estas listas de palabras, ya que se han hecho grandes esfuerzos para buscar en la web y determinar las palabras más utilizadas para cada tipo de confuso. Algunas de las listas de palabras más utilizadas se pueden encontrar bajo el Github[Listas](https://github.com/danielmiessler/SecLists)Repositorio, que clasifica las listas de palabras en varios tipos de fuzzing, incluso incluyendo contraseñas de uso común, que luego utilizaremos para el forzamiento bruto de contraseña.

Dentro de nuestro pwnbox, podemos encontrar todo`SecLists`Repo disponible bajo`/opt/useful/SecLists`. La lista de palabras específica que utilizaremos para páginas y fuzzing de directorio es otra lista de palabras comúnmente utilizada llamada`directory-list-2.3`, y está disponible en varias formas y tamaños. Podemos encontrar el que usaremos en:

Fuzzing web

```
xnoxos@htb[/htb]$ locate directory-list-2.3-small.txt/opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt

```

Consejo: Echando un vistazo a esta lista de palabras, notaremos que contiene comentarios de derechos de autor al principio, que pueden considerarse como parte de la lista de palabras y desorden los resultados. Podemos usar lo siguiente en`ffuf`para deshacerse de estas líneas con el`-ic`bandera.