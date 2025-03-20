# Detección de ransomware

`Ransomware`Aproveche una variedad de técnicas para lograr sus objetivos. En el siguiente análisis, exploraremos dos de estos métodos, examinando su funcionamiento interno y explicando cómo detectarlos a través de los esfuerzos de monitoreo de redes.

1. `File Overwrite Approach`: Ransomware emplea esta táctica accediendo a archivos a través del protocolo SMB, encriptándolos y luego sobrescribiendo directamente los archivos originales con sus versiones cifradas (nuevamente a través del protocolo SMB). Los actores maliciosos detrás del ransomware prefieren este método para su eficiencia, ya que requiere menos acciones y deja menos rastro de su actividad. Para detectar este enfoque, los equipos de seguridad deben buscar operaciones excesivas de sobrescritura de archivos en el sistema.
    
    ![](https://academy.hackthebox.com/storage/modules/233/121.png)
    
2. `File Renaming Approach`: En este enfoque, los actores de ransomware usan el protocolo SMB para leer archivos, luego los encriptan y finalmente cambian el nombre de los archivos encriptados al agregar una extensión única (nuevamente a través del protocolo SMB), a menudo indicativo de la cepa de ransomware. El cambio de nombre señala que los archivos se han mantenido como rehenes, lo que facilita a los analistas y administradores reconocer un ataque. La detección implica el monitoreo de un número inusual de archivos renombrados con la misma extensión, particularmente aquellos asociados con variantes de ransomware conocidas.
    
    ![](https://academy.hackthebox.com/storage/modules/233/122.png)
    

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detección de ransomware

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/ransomware_open_rename_sodinokibi`
- **Índice Splunk relacionado**: `ransomware_open_rename_sodinokibi`
- **Splunk SourceType relacionado**: `bro:smb_files:json`

---

- **Directorio**: `/home/htb-student/module_files/ransomware_new_file_extension_ctbl_ocker`
- **Índice Splunk relacionado**: `ransomware_new_file_extension_ctbl_ocker`
- **Splunk SourceType relacionado**: `bro:smb_files:json`

---

# **Detección de ransomware con registros de Splunk y Zeek (sobrescritura excesiva)**

Ahora exploremos cómo podemos identificar ransomware, utilizando registros de Splunk y Zeek.

Detección de ransomware

```
index="ransomware_open_rename_sodinokibi" sourcetype="bro:smb_files:json"
| where action IN ("SMB::FILE_OPEN", "SMB::FILE_RENAME")
| bin _time span=5m
| stats count by _time, source, action
| where count>30
| stats sum(count) as count values(action) dc(action) as uniq_actions by _time, source
| where uniq_actions==2 AND count>100

```

![](https://academy.hackthebox.com/storage/modules/233/123.png)

---

# **Detección de ransomware con registros de Splunk y Zeek (renombres excesivos con la misma extensión)**

Ahora exploremos cómo podemos identificar ransomware, utilizando registros de Splunk y Zeek.

Detección de ransomware

```
index="ransomware_new_file_extension_ctbl_ocker" sourcetype="bro:smb_files:json" action="SMB::FILE_RENAME"
| bin _time span=5m
| rex field="name" "\.(?<new_file_name_extension>[^\.]*$)"
| rex field="prev_name" "\.(?<old_file_name_extension>[^\.]*$)"
| stats count by _time, id.orig_h, id.resp_p, name, source, old_file_name_extension, new_file_name_extension,
| where new_file_name_extension!=old_file_name_extension
| stats count by _time, id.orig_h, id.resp_p, source, new_file_name_extension
| where count>20
| sort -count

```

![](https://academy.hackthebox.com/storage/modules/233/124.png)

**Desglose de búsqueda**:

- `index="ransomware_new_file_extension_ctbl_ocker" sourcetype="bro:smb_files:json" action="SMB::FILE_RENAME"`: Esta línea filtra los eventos basados en el índice`ransomware_new_file_extension_ctbl_ocker`, un tipo de fuente específico`bro:smb_files:json`y la acción`SMB::FILE_RENAME`. Esto reduce efectivamente la búsqueda al archivo SMB de cambio de nombre de las acciones en el índice especificado.
- `| bin _time span=5m`: Esta línea agrupa los eventos en`5`Encentores de tiempo de minuto.
- `| rex field="name" "\.(?<new_file_name_extension>[^\.]*$)"`: Esta línea usa la expresión regular (regex) para extraer la extensión del archivo de la`name`campo y lo asigna al nuevo campo`new_file_name_extension`.
- `| rex field="prev_name" "\.(?<old_file_name_extension>[^\.]*$)"`: Del mismo modo, esta línea extrae la extensión del archivo desde el`prev_name`campo y lo asigna al nuevo campo`old_file_name_extension`.
- `| stats count by _time, id.orig_h, id.resp_p, name, source, old_file_name_extension, new_file_name_extension`: Esta línea agrega los eventos y cuenta las ocurrencias basadas en varios campos, incluido el tiempo, el host de origen, el puerto de respuesta, el nombre del archivo, la fuente, la extensión de archivo anterior y la extensión de nuevo archivo.
- `| where new_file_name_extension!=old_file_name_extension`: Esta línea filtra eventos donde la nueva extensión del archivo es la misma que la extensión de archivo anterior.
- `| stats count by _time, id.orig_h, id.resp_p, source, new_file_name_extension`: Esta línea cuenta los eventos restantes por tiempo, el host de origen, el puerto de respuesta, la fuente y la nueva extensión de archivos.
- `| where count>20`: Esta línea filtra cualquier resultado con menos que`21`renombres de archivo dentro de un`5`La de tiempo de minuto.
- `| sort -count`: Esta línea clasifica los resultados en orden descendente basado en el recuento de renombes de archivos.

---

**Nota**: Las extensiones relacionadas con el ransomware conocidas se pueden encontrar en los recursos a continuación.

- [https://docs.google.com/spreadsheets/d/e/2pacx-1vrcvzg9jczak3hnqqrvctqqizh0ty77bwilebdu-q9oxkhaamqnlygtq4gf85pf6j6g3gmqxivuVoVo1u/pubHtml](https://docs.google.com/spreadsheets/d/e/2PACX-1vRCVzG9JCzak3hNqqrVCTQQIzH0ty77BWiLEbDu-q9oxkhAamqnlYgtQ4gF85pF6j6g3GmQxivuvO1U/pubhtml)
- [https://github.com/corelight/detect-ransomware-filenames](https://github.com/corelight/detect-ransomware-filenames)
- [https://fsrm.experiant.ca/](https://fsrm.experiant.ca/)