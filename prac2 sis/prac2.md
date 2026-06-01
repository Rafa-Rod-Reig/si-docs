PRACTICA 2 SISTEMAS

Cambio el nombre del servidor con 

![alt text](image.png)

lo compruebo tras reiniciar la máquina

![alt text](image-1.png)

El servidor srv-ad01 actuará como controlador de dominio (Domain Controller). Su función es centralizar la autenticación y autorización de todos los usuarios y equipos de la red. Gestiona el directorio de objetos del dominio (usuarios, equipos, grupos) y aplica las políticas de seguridad mediante GPO.

📎 https://learn.microsoft.com/es-es/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview

Instalo Active Directory desde el administrador de servidor

![alt text](image-4.png)

![alt text](image-5.png)

Selecciono añadir un nuevo bosque

![alt text](image-8.png)

Añado una contraseña

![alt text](image-9.png)

![alt text](image-10.png)

![alt text](image-11.png)

lo instalo

![alt text](image-12.png)

![alt text](image-13.png)

AD DS es el rol que convierte Windows Server en un controlador de dominio. Proporciona el servicio de directorio que almacena información sobre objetos de red (usuarios, equipos, impresoras…) y permite autenticar y autorizar el acceso a los recursos. También es la base sobre la que funcionan las GPO, los perfiles móviles y la administración centralizada.
📎 https://learn.microsoft.com/es-es/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview

Convertir un servidor en controlador de dominio significa que pasa a gestionar el dominio: autentica los inicios de sesión, almacena el directorio de Active Directory y replica la información con otros controladores si los hubiera. El DSRM (Directory Services Restore Mode) es un modo de arranque especial que permite reparar o restaurar la base de datos de Active Directory en caso de corrupción o fallo grave, sin que el servicio esté activo.
📎 https://learn.microsoft.com/es-es/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services

Inicio sesión tras el reinicio

![alt text](image-14.png)

Aparece el dominio empresa.local

![alt text](image-15.png)

La estructura básica del dominio empresa.local contiene estos contenedores por defecto:

Builtin — grupos locales predefinidos del sistema (Administrators, Users, Guests…)
Computers — equipos que se unen al dominio quedan aquí por defecto
Domain Controllers — OU donde se registra el propio servidor srv-ad01
Users — usuarios y grupos de dominio predeterminados (Administrator, Guest, Domain Admins…)

📎 https://learn.microsoft.com/es-es/windows-server/identity/ad-ds/plan/creating-a-site-design

creo a usuario1

![alt text](image-16.png)

![alt text](image-17.png)

Y a usuario2

![alt text](image-18.png)

Un usuario local existe únicamente en el equipo donde se crea; solo puede iniciar sesión en esa máquina y acceder a sus recursos locales. Un usuario de dominio se almacena en Active Directory y puede iniciar sesión en cualquier equipo del dominio, accediendo a los recursos de toda la red con las mismas credenciales. En entornos empresariales se usan usuarios de dominio porque permiten administración centralizada.
📎 https://learn.microsoft.com/es-es/windows-server/identity/ad-ds/manage/understand-default-user-accounts

![alt text](image-19.png)

creo la carpeta perfiles

![alt text](image-21.png)

![alt text](image-20.png)

La carpeta C:\perfiles compartida como \\srv-ad01\perfiles servirá como almacén centralizado de los perfiles de usuario. Cuando un usuario inicie sesión en cualquier equipo del dominio, su perfil (escritorio, documentos, configuración…) se cargará desde esta carpeta del servidor, garantizando que siempre tenga su entorno personalizado independientemente del equipo que use. Los permisos deben permitir escritura a cada usuario sobre su propia subcarpeta.
📎 https://learn.microsoft.com/es-es/windows-server/storage/folder-redirection/deploy-roaming-user-profiles

Configuro la ruta de acceso al perfil

![alt text](image-22.png)

![alt text](image-23.png)

Un perfil móvil (roaming profile) es un perfil de usuario almacenado en un servidor de red en lugar de en el equipo local. Al configurar la ruta \\srv-ad01\perfiles\%username%, Windows copia el perfil del servidor al equipo al iniciar sesión y lo sincroniza de vuelta al cerrarla. Las ventajas son: el usuario tiene siempre el mismo entorno en cualquier equipo del dominio, los datos se almacenan centralizadamente facilitando las copias de seguridad, y si el equipo falla el perfil no se pierde.
📎 https://learn.microsoft.com/es-es/windows-server/storage/folder-redirection/deploy-roaming-user-profiles


Configuro la red en el cliente

![alt text](image-24.png)

Y del servidor

![alt text](image-25.png)

compruebo que hace ping del cliente al servidor

![alt text](image-26.png)

Cambio el nombre del equipo cliente

![alt text](image-27.png)

Lo añado poniendo su nombre (usuario1) y la contraseña que le configuré

![alt text](image-28.png)

luego reinicio

Unir un equipo a un dominio significa registrarlo en Active Directory para que pase a estar gestionado por el controlador de dominio. A partir de ese momento, los usuarios del dominio pueden iniciar sesión en él, se le aplican las GPO del dominio y sus recursos pueden integrarse en la red corporativa. La diferencia con un grupo de trabajo es que en workgroup cada equipo gestiona sus propios usuarios de forma independiente, mientras que en dominio la gestión es centralizada en el DC.
📎 https://learn.microsoft.com/es-es/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview


inicio sesión con usuario1

![alt text](image-29.png)

me da error porque el perfil no es movil

![alt text](image-31.png)

![alt text](image-32.png)

corrijo los permisos NFTS

![alt text](image-33.png)

corrijo la ruta de los perfiles ya que le faltaba las barras invertidas

![alt text](image-34.png)

inicio sesión con usuario1

![alt text](image-36.png)

Y con usuario2

![alt text](image-35.png)

Se han creado las carpetas de los respectivos usuarios en la carpeta Perfiles

![alt text](image-37.png)

Al iniciar sesión por primera vez, Windows crea la carpeta del perfil en \\srv-ad01\perfiles\usuario1 y carga el perfil desde el servidor (la primera vez crea uno nuevo). Al cerrar sesión, sincroniza los cambios al servidor. Si el mismo usuario inicia sesión en otro equipo del dominio, descarga ese perfil guardado, teniendo exactamente el mismo escritorio y configuración. Esto se verifica porque en el servidor aparecen las carpetas usuario1 y usuario2 dentro de perfiles.
📎 https://learn.microsoft.com/es-es/windows-server/storage/folder-redirection/deploy-roaming-user-profiles

Localizo la directiva

![alt text](image-39.png)

edito la longitud mínima de contraseña

![alt text](image-44.png)

Las Group Policy Objects (GPO) son conjuntos de configuraciones que el administrador aplica de forma centralizada a usuarios y equipos del dominio. Permiten controlar desde la política de contraseñas hasta la configuración del escritorio, el acceso a aplicaciones, la instalación de software o la configuración de red. Se gestionan desde la herramienta Group Policy Management y se aplican siguiendo la jerarquía del dominio (sitio → dominio → OU).
📎 https://learn.microsoft.com/es-es/windows-server/identity/ad-ds/manage/group-policy/group-policy-overview


Ejecuto gpupdate desde usuario1

![alt text](image-42.png)

Al intentar cambiar la contraseña y poner una de menos de 8 carácteres no me deja

![alt text](image-45.png)

Las políticas de dominio se aplican automáticamente al iniciar sesión y se actualizan en segundo plano cada 90 minutos aproximadamente. El comando gpupdate /force fuerza la actualización inmediata sin necesidad de reiniciar. Al intentar cambiar la contraseña con la nueva política activa, el sistema rechazará las contraseñas que no cumplan los requisitos configurados (longitud mínima, complejidad…), confirmando que la GPO se ha aplicado correctamente.
📎 https://learn.microsoft.com/es-es/windows-server/identity/ad-ds/manage/group-policy/group-policy-overview


COMPROBACIONES

![alt text](image-46.png)

 cli01 pertenece al dominio y usuario1 ha iniciado sesión
En esta captura se puede observar la pantalla de información del sistema del equipo cliente. En la parte superior izquierda se aprecia que la sesión está iniciada con la cuenta usuario1@empresa.local, lo que confirma que el usuario de dominio ha podido autenticarse correctamente. En el campo "Nombre completo del dispositivo" aparece DESKTOP-7B7730T.empresa.local, lo que demuestra que el equipo está unido al dominio empresa.local y es reconocido por el controlador de dominio.

![alt text](image-47.png)

En esta captura se muestra el contenido de la carpeta C:\perfiles del servidor srv-ad01. Se pueden ver dos carpetas creadas automáticamente por Windows al iniciar sesión cada usuario: uduario1.V6 y usuario2.V6. El sufijo .V6 indica que son perfiles de versión 6, correspondiente a Windows 10/11. Estas carpetas fueron creadas el 20/05/2026 a las 13:36, momento en el que los usuarios iniciaron sesión por primera vez en el cliente cli01. Esto confirma que los perfiles móviles están funcionando correctamente y se almacenan de forma centralizada en el servidor.

![alt text](image-48.png)

En esta captura se muestra la ejecución del comando gpupdate /force en PowerShell del cliente cli01. Se pueden observar dos situaciones:

Primer intento fallido: se escribió gpupdate/force sin espacio, por lo que PowerShell no reconoce el comando y devuelve un error.
Intentos correctos: al escribirlo correctamente con espacio (gpupdate /force), el sistema responde con "Actualizando directiva..." y confirma que tanto la directiva de equipo como la de usuario se han actualizado correctamente.

Esto confirma que el cliente está recibiendo y aplicando las GPO configuradas en el servidor, incluyendo la política de contraseñas modificada en el ejercicio 10.