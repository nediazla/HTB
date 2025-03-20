# Identifying IDORs

# **Parámetros de URL y API**

---

El primer paso de explotar las vulnerabilidades IDOR es identificar referencias de objetos directos. Siempre que recibamos un archivo o recurso específico, debemos estudiar las solicitudes HTTP para buscar parámetros de URL o API con una referencia de objeto (por ejemplo,`?uid=1`o`?filename=file_1.pdf`). Estos se encuentran principalmente en parámetros o API de URL, pero también se pueden encontrar en otros encabezados HTTP, como las cookies.

En los casos más básicos, podemos intentar incrementar los valores de las referencias del objeto para recuperar otros datos, como (`?uid=2`) o (`?filename=file_2.pdf`). También podemos usar una aplicación borrosa para probar miles de variaciones y ver si devuelven algún dato. Cualquier éxito exitoso a los archivos que no sean nuestros propios indicaría una vulnerabilidad IDOR.

---

# **AJAX Llamadas**

También podemos identificar parámetros o API no utilizados en el código frontal en forma de llamadas JavaScript AJAX. Algunas aplicaciones web desarrolladas en JavaScript Frameworks pueden realizar insegurosamente todas las llamadas de funciones en el front-end y usar las apropiadas en función del rol del usuario.

Por ejemplo, si no tuviéramos una cuenta de administración, solo se utilizarían las funciones a nivel de usuario, mientras que las funciones de administración se deshabilitarían. Sin embargo, es posible que aún podamos encontrar las funciones de administración si analizamos el código JavaScript frontal y podemos identificar llamadas AJAX a puntos finales o API específicos que contienen referencias de objetos directos. Si identificamos las referencias de objetos directos en el código JavaScript, podemos probarlas para obtener vulnerabilidades IDOR.

Esto no es exclusivo de las funciones de administración, por supuesto, pero también puede ser ninguna función o llamadas que no se encuentren mediante el monitoreo de las solicitudes HTTP. El siguiente ejemplo muestra un ejemplo básico de una llamada AJAX:

Código:javascript

```jsx
function changeUserPassword() {
    $.ajax({
        url:"change_password.php",
        type: "post",
        dataType: "json",
        data: {uid: user.uid, password: user.password, is_admin: is_admin},
        success:function(result){
            //
        }
    });
}

```

Es posible que nunca se llame a la función anterior cuando usamos la aplicación web como usuario no administrativo. Sin embargo, si lo ubicamos en el código frontal, podemos probarlo de diferentes maneras para ver si podemos llamarlo para realizar cambios, lo que indicaría que es vulnerable a IDOR. Podemos hacer lo mismo con el código de fondo si tenemos acceso a él (por ejemplo, aplicaciones web de código abierto).

---

# **Comprender el hash/codificación**

Algunas aplicaciones web pueden no usar números secuenciales simples como referencias de objetos, pero pueden codificar la referencia o en su lugar. Si encontramos tales parámetros utilizando valores codificados o hash, es posible que aún podamos explotarlos si no hay un sistema de control de acceso en el back-end.

Supongamos que la referencia fue codificada con un codificador común (por ejemplo,`base64`). En ese caso, podríamos decodificarlo y ver el texto sin formato de la referencia del objeto, cambiar su valor y luego codificarlo nuevamente para acceder a otros datos. Por ejemplo, si vemos una referencia como (`?filename=ZmlsZV8xMjMucGRm`), podemos adivinar inmediatamente que el nombre del archivo es`base64`codificado (desde su conjunto de caracteres), que podemos decodificar para obtener la referencia del objeto original de (`file_123.pdf`). Luego, podemos intentar codificar una referencia de objeto diferente (por ejemplo,`file_124.pdf`) e intente acceder a él con la referencia de objeto codificado (`?filename=ZmlsZV8xMjQucGRm`), que puede revelar una vulnerabilidad IDOR si pudiéramos recuperar cualquier dato.

Por otro lado, la referencia del objeto puede ser hash, como (`download.php?filename=c81e728d9d4c2f636f067f89cc14862c`). A primera vista, podemos pensar que esta es una referencia de objeto segura, ya que no está utilizando ningún texto claro o codificación fácil. Sin embargo, si observamos el código fuente, podemos ver lo que se está hashado antes de que se realice la llamada API:

Código:javascript

```jsx
$.ajax({
    url:"download.php",
    type: "post",
    dataType: "json",
    data: {filename: CryptoJS.MD5('file_1.pdf').toString()},
    success:function(result){
        //
    }
});

```

En este caso, podemos ver que el código usa el`filename`Y hashinglo con`CryptoJS.MD5`, facilitándonos calcular el`filename`para otros archivos potenciales. De lo contrario, podemos tratar manualmente de identificar el algoritmo de hash que se está utilizando (por ejemplo, con herramientas de identificador hash) y luego hash el nombre de archivo para ver si coincide con el hash usado. Una vez que podamos calcular hashes para otros archivos, podemos intentar descargarlos, lo que puede revelar una vulnerabilidad IDOR si podemos descargar cualquier archivo que no nos pertenezca.

---

# **Comparar roles de usuario**

Si queremos realizar ataques IDOR más avanzados, es posible que necesitemos registrar múltiples usuarios y comparar sus solicitudes HTTP y referencias de objetos. Esto puede permitirnos comprender cómo se calculan los parámetros de URL y los identificadores únicos y luego calcularlos para que otros usuarios recopilen sus datos.

Por ejemplo, si tuviéramos acceso a dos usuarios diferentes, uno de los cuales puede ver su salario después de hacer la siguiente llamada API:

Código:json

```json
{
  "attributes" :
    {
      "type" : "salary",
      "url" : "/services/data/salaries/users/1"
    },
  "Id" : "1",
  "Name" : "User1"

}

```

El segundo usuario puede no tener todos estos parámetros de API para replicar la llamada y no debería poder hacer la misma llamada que`User1`. Sin embargo, con estos detalles a la mano, podemos intentar repetir la misma llamada API mientras registramos como`User2`Para ver si la aplicación web devuelve algo. Dichos casos pueden funcionar si la aplicación web solo requiere una sesión válida iniciada para realizar la llamada API, pero no tiene control de acceso en el back-end para comparar la sesión de la persona que llama con los datos que se llaman.

Si este es el caso, y podemos calcular los parámetros API para otros usuarios, esta sería una vulnerabilidad IDOR. Incluso si no pudiéramos calcular los parámetros API para otros usuarios, aún habríamos identificado una vulnerabilidad en el sistema de control de acceso de fondo y podemos comenzar a buscar otras referencias de objetos para explotar.