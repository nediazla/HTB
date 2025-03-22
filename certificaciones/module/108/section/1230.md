# Descripción general del escaneo de vulnerabilidad

Como se discutió anteriormente, el escaneo de vulnerabilidad se realiza para identificar vulnerabilidades potenciales en dispositivos de red como enrutadores, firewalls, conmutadores, así como servidores, estaciones de trabajo y aplicaciones. El escaneo está automatizado y se centra en encontrar potencial/vulnerabilidades conocidas en la red o en el nivel de aplicación.`Vulnerabilities scanners typically do not exploit vulnerabilities (with some exceptions) but need a human to manually validate scan issues`Para determinar si un escaneo particular devolvió o no problemas reales que necesitan ser solucionados o falsos positivos que puedan ser ignorados y excluidos de los escaneos futuros contra el mismo objetivo.

El escaneo de vulnerabilidad a menudo es parte de una prueba de penetración estándar, pero los dos no son lo mismo. Un escaneo de vulnerabilidad puede ayudar a obtener cobertura adicional durante una prueba de penetración o acelerar las pruebas del proyecto bajo limitaciones de tiempo. Una prueba de penetración real incluye mucho más que un solo escaneo.

El tipo de escaneos que se ejecuta varía de una herramienta a otra, pero la mayoría de las herramientas`run a combination of dynamic and static tests`, dependiendo del objetivo y la vulnerabilidad. A`static test`Determinaría una vulnerabilidad si la versión identificada de un activo particular tiene una CVE pública. Sin embargo, esto no siempre es exacto, ya que puede haberse aplicado un parche, o el objetivo no es específicamente vulnerable a ese CVE. Por otro lado, un`dynamic test`intenta cargas útiles específicas (generalmente benignas), como credenciales débiles, inyección de SQL o inyección de comandos en el objetivo (es decir, una aplicación web). Si alguna carga útil devuelve un éxito, entonces hay una buena posibilidad de que sea vulnerable.

Las organizaciones deben ejecutar ambas`unauthenticated and authenticated scans`En un cronograma continuo para garantizar que los activos se parcen a medida que se descubren nuevas vulnerabilidades y que los activos nuevos agregados a la red no tengan parches faltantes u otros problemas de configuración/parche. El escaneo de vulnerabilidad debe alimentarse con el[gestión de parches](https://en.wikipedia.org/wiki/Patch_(computing))programa.

`Nessus`, `Nexpose`, y`Qualys`Son plataformas de escaneo de vulnerabilidad bien conocidas que también proporcionan ediciones comunitarias gratuitas. También hay alternativas de código abierto como`OpenVAS`.

---

# **Descripción general de Nessus**

[Nessus Essentials](https://community.tenable.com/s/article/Nessus-Essentials)Por Tenable es la versión gratuita del escáner oficial de vulnerabilidad de Nessus. Las personas pueden acceder a Nessus Essentials para comenzar a comprender el escáner de vulnerabilidades de Tenable. La advertencia es que solo se puede usar para hasta 16 hosts. Las características de la versión gratuita son limitadas, pero son perfectas para alguien que busca comenzar con Nessus. El escáner libre intentará identificar vulnerabilidades en un entorno.

![](https://academy.hackthebox.com/storage/modules/108/Nessus_Essentials___Folders___View_Scan.png)

---

# **Descripción general de OpenVas**

[Abierto](https://www.openvas.org/)por Greenbone Networks es un escáner de vulnerabilidad de código abierto disponible públicamente. OpenVas puede realizar escaneos de red, incluidas pruebas autenticadas y no autenticadas.

![](https://academy.hackthebox.com/storage/modules/108/openvas/dashboard.png)