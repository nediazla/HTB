# Hunting Evil con Yara (edición web)

[Impac.me](https://unpac.me/)Se adapta la herramienta para el desempaquetado de malware. Lo mejor de`Unpac.Me`es que nos otorga la capacidad de ejecutar nuestras reglas de Yara sobre su base de datos acumulada de presentaciones de malware. Teniendo en cuenta los obstáculos de obtener acceso a conjuntos de datos de malware comercializados, Unpac.ME emerge como un activo principal para esos analistas de SOC dedicados y entusiastas persistentes de malware.

# **Huntando por el mal dentro de los conjuntos de datos en línea con Yara**

Supongamos que queremos probar la siguiente regla de Yara.

Código:Yara

```
rule ransomware_dharma {

    meta:
        author = "Madhukar Raina"
        version = "1.0"
        description = "Simple rule to detect strings from Dharma ransomware"
        reference = "https://www.virustotal.com/gui/file/bff6a1000a86f8edf3673d576786ec75b80bed0c458a8ca0bd52d12b74099071/behavior"

    strings:
        $string_pdb = {  433A5C6372797369735C52656C656173655C5044425C7061796C6F61642E706462 }
	$string_ssss = { 73 73 73 73 73 62 73 73 73 }

        condition: all of them
}

```

Entonces, ¿cómo comenzamos?

- Regístrese para el acceso de costo cero y suba a la plataforma.
- Dirigirse a`Yara Hunt`y elegir`New Hunt`.
    
    ![](https://academy.hackthebox.com/storage/modules/234/unpac1.png)
    
- Ingrese la regla Yara en el espacio de reglas designado.
    
    ![](https://academy.hackthebox.com/storage/modules/234/unpac33.png)
    
- Primer éxito`Validate`y luego`Scan`.
    
    ![](https://academy.hackthebox.com/storage/modules/234/unpac44.png)
    
- Los resultados del escaneo se muestran justo en frente de nuestros ojos. Echando un vistazo rápido a nuestro ejemplo, el sistema se apresuró a través de todas las presentaciones de malware en un par de minutos, detectando 1 partido.
    
    ![](https://academy.hackthebox.com/storage/modules/234/unpac55.png)
    

Para individuos y organizaciones con recursos limitados, Unpac.ME y plataformas similares pueden servir como peldaños para mejorar sus capacidades de análisis y detección de malware, lo que les permite hacer contribuciones significativas al campo de la ciberseguridad.