# Web Mass Assignment Vulnerabilities

Varios marcos ofrecen prácticas características de asignación de masa para disminuir la carga de trabajo para los desarrolladores. Debido a esto, los programadores pueden insertar directamente un conjunto completo de datos ingresados por el usuario de un formulario en un objeto o base de datos. Esta característica a menudo se usa sin una lista blanca para proteger los campos de la entrada del usuario. Esta vulnerabilidad podría ser utilizada por un atacante para robar información confidencial o destruir datos.

La vulnerabilidad de la asignación de masa web es un tipo de vulnerabilidad de seguridad donde los atacantes pueden modificar los atributos del modelo de una aplicación a través de los parámetros enviados al servidor. Al revertir el código, los atacantes pueden ver estos parámetros y asignando valores a parámetros críticos sin protección durante la solicitud HTTP, pueden editar los datos de una base de datos y cambiar la funcionalidad prevista de una aplicación.

Ruby on Rails es un marco de aplicaciones web que es vulnerable a este tipo de ataque. El siguiente ejemplo muestra cómo los atacantes pueden explotar la vulnerabilidad de la asignación de masa en Ruby on Rails. Suponiendo que tengamos un`User`modelo con los siguientes atributos:

Código:rubí

```ruby
class User < ActiveRecord::Base
  attr_accessible :username, :email
end

```

El modelo anterior especifica que solo el`username`y`email`Los atributos pueden ser asignados en masa. Sin embargo, los atacantes pueden modificar otros atributos al manipular los parámetros enviados al servidor. Supongamos que el servidor recibe los siguientes parámetros.

Código:javascript

```jsx
{ "user" => { "username" => "hacker", "email" => "hacker@example.com", "admin" => true } }

```

Aunque el`User`el modelo no afirma explícitamente que el`admin`El atributo es accesible, el atacante aún puede cambiarlo porque está presente en los argumentos. Evitando cualquier control de acceso que pueda estar en su lugar, el atacante puede enviar estos datos como parte de una solicitud de publicación al servidor para establecer un usuario con privilegios de administración.

---

# **Explotación de la vulnerabilidad de asignación de masa**

Supongamos que nos encontramos con la siguiente aplicación que presenta una aplicación web de Asset Manager. También suponga que se nos ha proporcionado el código fuente de la aplicación. Completando el paso de registro, recibimos el mensaje`Success!!`, y podemos intentar iniciar sesión.

![](https://academy.hackthebox.com/storage/modules/113/mass_assignment/pending.png)

Después de iniciar sesión, recibimos el mensaje`Account is pending approval`. El administrador de esta aplicación web debe aprobar nuestro registro. Revisión del código de Python del`/opt/asset-manager/app.py` file reveals the following snippet.

Código:pitón

```python
for i,j,k in cur.execute('select * from users where username=? and password=?',(username,password)):
  if k:
    session['user']=i
    return redirect("/home",code=302)
  else:
    return render_template('login.html',value='Account is pending for approval')

```

Podemos ver que la aplicación está verificando si el valor`k`está configurado. En caso afirmativo, permite al usuario iniciar sesión. En el código a continuación, también podemos ver que si establecemos el`confirmed`parámetro durante el registro, luego se inserta`cond`como`True`y nos permite evitar el paso de verificación de registro.

Código:pitón

```python
try:
  if request.form['confirmed']:
    cond=True
except:
      cond=False
with sqlite3.connect("database.db") as con:
  cur = con.cursor()
  cur.execute('select * from users where username=?',(username,))
  if cur.fetchone():
    return render_template('index.html',value='User exists!!')
  else:
    cur.execute('insert into users values(?,?,?)',(username,password,cond))
    con.commit()
    return render_template('index.html',value='Success!!')

```

En ese caso, lo que debemos probar es registrar a otro usuario e intentar configurar el`confirmed`parámetro a un valor aleatorio. Usando Burp Suite, podemos capturar la solicitud de publicación HTTP al`/register`página y establecer los parámetros`username=new&password=test&confirmed=test`.

![](https://academy.hackthebox.com/storage/modules/113/mass_assignment/mass_hidden.png)

Ahora podemos intentar iniciar sesión en la aplicación utilizando el`new:test`cartas credenciales.

![](https://academy.hackthebox.com/storage/modules/113/mass_assignment/loggedin.png)

La vulnerabilidad de asignación masiva se explota con éxito y ahora estamos iniciadas en la aplicación web sin esperar a que el administrador apruebe nuestra solicitud de registro.

---

# **Prevención**

Para evitar este tipo de ataque, uno debe asignar explícitamente los atributos para los campos permitidos, o usar métodos de lista blanca proporcionados por el marco para verificar los atributos que pueden ser asignados en masa. El siguiente ejemplo muestra cómo usar parámetros fuertes en el`User`controlador.

Código:rubí

```ruby
class UsersController < ApplicationController
  def create@user = User.new(user_params)
    if @user.save
      redirect_to @user
    else
      render 'new'
    end
  end

  private

  def user_params
    params.require(:user).permit(:username, :email)
  end
end

```

En el ejemplo anterior, el`user_params`El método devuelve un nuevo hash que incluye solo el`username`y`email`Atributos, ignorando más información que el cliente puede haber enviado. Al hacer esto, nos aseguramos de que solo los atributos permitidos explícitamente se puedan cambiar mediante asignación de masa.