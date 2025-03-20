# Creación de aplicaciones Splunk personalizadas

# **Cómo crear una aplicación Splunk personalizada**

1. `Access Splunk Web`: Abra su navegador web y navegue a Splunk Web.
2. `Go to Manage Apps`: De la barra de menú en la parte superior, seleccione`Apps`Y luego elige`Manage Apps`.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image46.png)
    
3. `Create a New App`: En el`Apps`página, haga clic en`Create app`.
4. `Enter App Details`: En el`Add new`página, complete las propiedades para su nueva aplicación:
    - `Name`: Ingrese el nombre de su aplicación, por ejemplo,`<Your app name>`.
    - `Folder name`: Especifique el nombre de la carpeta, que debería ser similar a`<App_name>`. Esto correspondirá al directorio de la aplicación en`$SPLUNK_HOME/etc/apps/`.
    - `Version`: Entrada "1.0.0".
    - `Description`: Proporcione una descripción para su aplicación, por ejemplo,`<App description>`.
    - `Template`: Elegir`barebones`Desde el menú desplegable.
        
        ![](https://academy.hackthebox.com/storage/modules/233/image20.png)
        
5. `Save the App`: Haga clic en`Save`. Puede verificar que su aplicación se haya creado yendo al`Apps`menú. Su nueva aplicación ahora debe estar en la lista allí. Además, si navega a la página de inicio web de Splunk, encontrará su aplicación en la lista de`Apps`enumerar como`Academy hackthebox - Detection of Active Directory Attacks`. 
    
    ![](https://academy.hackthebox.com/storage/modules/233/image7.png)
    
    ![](https://academy.hackthebox.com/storage/modules/233/image36.png)
    
6. `Explore the Directory Structure`: Use un navegador de archivo para navegar a`$SPLUNK_HOME/etc/apps`. Aquí encontrará su directorio de aplicaciones, que incluye las siguientes carpetas:
    - `/bin`: Aquí es donde se almacenan los guiones.
    - `/default`: Este directorio contiene archivos para configuración, vistas, paneles y navegación de aplicaciones.
    - `/local`: Este directorio contiene versiones modificadas por el usuario de archivos para configuración, vistas, paneles y navegación de aplicaciones.
    - `/metadata`: Este directorio contiene archivos de permisos.
        
        ![](https://academy.hackthebox.com/storage/modules/233/image30.png)
        
7. `View the Navigation File`: El archivo de configuración de navegación es un archivo XML. Usando un editor de texto, abra`$SPLUNK_HOME/etc/apps/<your app>/default/data/ui/nav/default.xml`. Aquí encontrará la definición de navegación predeterminada para una aplicación:
    
    Código:xml
    
    ```xml
      <nav search_view="search"><view name="search" default='true' /><view name="analytics_workspace" /><view name="datasets" /><view name="reports" /><view name="alerts" /><view name="dashboards" /></nav>
    ```
    
    En este XML, la etiqueta NAV de nivel superior actúa como padre. El`search_view`El atributo designa la vista predeterminada para las búsquedas. En este caso, el`search`Se emplea la vista, que se hereda de la`Search & Reporting`aplicación El siguiente nivel en la jerarquía XML corresponde a los elementos que se muestran en la barra de aplicaciones. La lista de etiquetas de vista denota diferentes vistas para mostrar. Cada una de las vistas corresponde a una vista desde la aplicación de búsqueda e informes. El atributo`default='true'`indica la vista de usar como la página de inicio de la aplicación: aquí, el`search`La vista sirve como la página de inicio.
    
8. `Create Your First Dashboard`: Ir a`dashboards`y haga clic en`Create New Dashboard`. Ingrese el nombre del tablero, proporcione una descripción si es necesario, configure los permisos y seleccione`Classic Dashboards`.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image50.png)
    
    ![](https://academy.hackthebox.com/storage/modules/233/image54.png)
    
9. `Configure the Dashboard`: Ahora verá la página del editor del tablero, donde puede configurar paneles, entradas, etc., para facilitar su proceso de monitoreo. Agregue la entrada de tiempo para el tablero y ajuste el rango de tiempo predeterminado para satisfacer sus necesidades. A continuación, agregue un panel de tabla estadística, seleccione un rango de tiempo para el selector de tiempo compartido, agregue el título de contenido (por ejemplo,`"<Panel name>"`), e ingrese la cadena de búsqueda. Para usar la entrada en las búsquedas, encienda el token de entrada con signos de dólar, como`$user$`. Hacer clic`Add to Dashboard`Cuando esté listo. Guarde sus cambios.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image4.png)
    
    ![](https://academy.hackthebox.com/storage/modules/233/image83.png)
    
    ![](https://academy.hackthebox.com/storage/modules/233/image27.png)
    
    ![](https://academy.hackthebox.com/storage/modules/233/image12.png)
    
10. `Dashboard Storage`: Todos los paneles que ha creado en su aplicación se almacenan en`"<AppPath>/local/data/ui/views/dashboard_title.xml"`. Para agregar su tablero a la barra de navegación, simplemente agregue el título del tablero a la página predeterminada de navegación XML:`"<AppPath>/local/data/ui/nav/default.xml"`.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image21.png)
    
11. `Restart Splunk`: Reinicie su instancia de Splunk. Una vez reiniciado, debería ver su tablero en la barra de navegación.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image55.png)
    
12. `Grouping Dashboards`: Si desea agrupar múltiples paneles bajo una sola entrada en la barra de navegación, use la etiqueta de colección.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image58.png)
    

# **Actualización y exploración de la aplicación Splunk "Academy HackTheBox - Detección de ataques Active Directory"**

`Detection-of-Active-Directory-Attacks.tar.gz.tar`se puede descargar del`Resources`sección de este módulo (esquina superior derecha) y se usa para actualizar el existente`Academy hackthebox - Detection of Active Directory Attacks`Aplicación Splunk haciendo clic`Apps` -> `Manage Apps` -> `Install app from file` -> `Browse` -> ✓ `Upgrade app. Checking this will overwrite the app if it already exists.` -> `Upload`.

Ahora, tómese un tiempo para explorar esta aplicación Splunk personalizada y vea cómo puede mejorar significativamente nuestras capacidades de monitoreo.

[Anterior](https://academy.hackthebox.com/module/233/section/2531)