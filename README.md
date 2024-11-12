<h1>Microsoft Sentinel Mapa de Ataque Home Lab</h1>

<h2>Descripción</h2>
<b>En este lab muestro un paso a paso de como usar una cuenta de microsoft azure, para crear una maquina virtual vulnerable con windows 10 que va a servir como objetivo facil para atacantes. La idea es exponer esta maquina virtual al internet y luego usar el monitor de agentes de azure,Microsoft Defender for Cloud, y Azure Sentinel para recolectar y agregar la data del ataque y mostrarla desplegada en un mapa de Microsoft Sentinel. En este proyecto voy a demostrar el uso de diferentes recursos y herramientas. Voy a estar usando Powershell para escanear el visor de eventos de windows en la maquina expuesta, mas especificamente el EventID 4625 que es intento de inicio de sesion fallido, y enviar esa data a un logfile.El script de Powershell tambien envia la direccion IP de cualquier inicio de sesion fallido a IPgeolocation.io por medio de una API, para asi luego esa informacion pueda ser usada por Microsoft Sentinel para mapear de donde se originaron esos intentos. Este proyecto fue realizado con la idea de ganar experiencia practica con SIEMs, conceptos de la nube y recursos, APIs, y Microsoft Azure en general. Aprendí como configurar y manejar recursos en la nube, como leer SIEM logs y poder extraer data de ellos.<b/>
<br />

<h2>Herramientas Usadas</h2>

- <b>Microsoft Sentinel (SIEM)</b> 
- <b>Log Analytic Workbooks</b>
- <b>Microsoft Defender for Cloud</b>
- <b>Virtual Machines</b>
- <b>Remote Desktop</b>
- <b>PowerShell</b>
- <b>API's</b>
- <b>Event Viewer</b>
- <b>Firewalls</b>

<h2>Entornos Usados </h2>

- <b>Microsoft Azure</b>
- <b>Windows 10 Pro</b>

<h2>Links</h2>

- <b>Microsoft Azure Free Trial:</b> https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account/
- <b>IPGeolocation:</b> https://ipgeolocation.io/

<h2 align="center">Paso a paso del Proyecto</h2>

<p align="center">
<b>Lo primero que hay que hacer una vez ya creada la cuenta de Azure, es crear un grupo de recursos, dentro de este va a estar montado el laboratorio, esto facilita la eliminación posteriormente del mismo (ya que nos seguiria consumiendo los creditos gratuitos de la prueba)  debido a que de otra manera tendriamos que eliminar cada maquina,vnet, servidor o lo que se este usando, uno por uno, de esta manera ahorramos tiempo y tambien es mas facil de manejar cada recurso. Para encontrar el grupo de recursos podemos buscarlo en la barra superior. Al momento de elegir la zona, la predeterminada siempre es US East, pero aca debemos elegir la que es mas cercana a donde estemos. </b> <br/>
</p>


![image](https://github.com/user-attachments/assets/dddc445a-a85f-4660-a064-df218a9d2e24)
![image](https://github.com/user-attachments/assets/37742e4c-4950-4430-9e6a-66908b400869)
![image](https://github.com/user-attachments/assets/fb6aaa05-3aa7-4ea1-8504-749f517ef3be)
![image](https://github.com/user-attachments/assets/d801a100-4b5a-4a57-9e66-96932272063d)








<br />
<br />
<p align="center">
<b>Lo siguiente que voy a hacer es  crear la máquina virtual que va a actuar de honeypot. A la hora de elegir el grupo de recurso, seleccionen el que crearon recientemente.</p>

![image](https://github.com/user-attachments/assets/da77ff8a-0222-4545-857d-eaea63d7d46a)
![image](https://github.com/user-attachments/assets/02ac0815-8e6d-4c48-a3e5-6367c311d499)
![image](https://github.com/user-attachments/assets/bc9c9f6a-b08c-49ce-916b-1a81ed7dcd8a)





<p align="center"> Recomiendo elegir 2 vcpu, que es lo suficiente que la maquina necesita para no crashear.</p>

![image](https://github.com/user-attachments/assets/8ebd2c1a-1671-427b-b06f-b0360870243f)

 <p align="center">Cuando vayan a crear el usario y contraseña de la maquina virtual no lo olviden porque con esta informacion vamos a entrar de manera remota a la maquina posteriormente.</p>

![image](https://github.com/user-attachments/assets/33e555a7-a0c0-4678-9dfa-f880e49f69c1)

<p align="center"> En esta seccion seleccionen proximo hasta llegar al apartado de networking.</p>

![image](https://github.com/user-attachments/assets/3f4c9589-0db3-420a-8457-2461b33fae92)


 <p align="center">El proximo paso es crear y configurar un nuevo Network Security Group (NSG) que vendria a actuar como nuestro firewall. En este caso no queremos ninguna regla que limite y bloquee tráfico ya que queremos permitir que cualquiera se pueda comunicar con nuestra máquina, La configuracion de la nueva regla que creamos es para que permita cualquier puerto (*)</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/6394b8a0-db20-4eab-9305-fef735ce99fd)
![image](https://github.com/user-attachments/assets/72f33793-e8fe-4835-b49d-5900f0d94bb8)
![image](https://github.com/user-attachments/assets/79f4727e-ce7d-4e6c-8f87-d0db33818a49)
![image](https://github.com/user-attachments/assets/6d8d46c0-20ca-4ece-af44-317d14a2f57a)
![image](https://github.com/user-attachments/assets/f111f593-7ca5-4396-b4f9-8a4e74c004bd)
![image](https://github.com/user-attachments/assets/c2172f70-55ff-4925-a404-231896192c1f)


























<br />
<br />

<p align="center">
<b>Mientras se crea la máquina virtual,podemos empezar creando nuestro Log Analytics Workspace. Recuerden elegir siempre el grupo de recursos creado para este lab  .</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/84529b87-e698-4328-b032-04c547ab3b05)
![image](https://github.com/user-attachments/assets/d83ba323-affc-492a-b9df-9c8bddd9b7c4)
![image](https://github.com/user-attachments/assets/db0d9768-212f-4731-a79b-6ac1a62f8819)
![image](https://github.com/user-attachments/assets/ef5f6ac7-5723-4131-a646-479a51668c62)





<br />
<br />
<p align="center">
<b>Ahora nos dirigimos a Microsoft Defender for Cloud para acceder a un plan que nos permita tomar y agregar data para nuestro SIEM, Microsoft Sentinel, para usar luego.
</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/38aff52f-f412-4069-9e1e-952e7c61fbe3)
![image](https://github.com/user-attachments/assets/7885fd07-f0fd-4f2d-a9e0-e0d8e734b591)
![image](https://github.com/user-attachments/assets/d1aa3714-c391-428b-99a7-dcdce57813b8)
![image](https://github.com/user-attachments/assets/fa2ad8cc-c88c-40ef-9882-7b39454ecfa5)












<br />
<p align="center">
<b>Una vez ya creada la maquina virtual, podemos ir al Log Analytics Workspaces y conectar nuestra VM al servicio  .</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/bf7784b7-3001-4fbb-a913-bbfdbe08b0e3)
![image](https://github.com/user-attachments/assets/0139f03a-ab84-4d39-87e3-e790b9434944)
![image](https://github.com/user-attachments/assets/4ce549a1-145e-49be-ac6c-2ef093bb99df)




<br />
<br />
<p align="center">
<b>Ahora debemos crear una instancia de Microsoft Sentinel y conectarla a nuestra VM. En mi caso la VM no aparece porque ya la conecte previamente, pero a ustedes les va a salir para seleccionar y luego la agregan </b> <br/>
</p>

![image](https://github.com/user-attachments/assets/e32f9b73-00c9-47e1-be01-812d1b734d0a)


<br />
<br />
<p align="center">
<b>Ahora que ya esta todo configurado en el dashboard de Azure, podemos entrar a la vm y configurarla, para eso necesitamos obtener la direccion IP para entrar via Escritorio Remoto.</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/56e07206-3a8b-46dd-88d0-6a3978d3af18)


<br />
<br />
<p align="center">
<b>Abrimos el escritorio remoto e ingresamos las credenciales que creamos cuando hicimos la VM .</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/7c018091-84da-427e-8294-8447f3bf9521)


<br />
<br />
<p align="center">
<b>Una vez ya logueado en la VM, nos dirigmos al visor de eventos de windows (Event Viewer), que es donde se registra todo lo que sucede en un sistema Windows.Le asigna a cada acción un EventID para que pueda ser facilmente navegado mediante el EventID, para este proyecto nos interesa principalmente el EventID 4625 que es el de Intentos de Logon Fallidos(Failed Logon Attemps).Estos logs los podemos encontrar en la pestaña de seguridadn, en mi caso tengo varios de estos intentos fallidos porque estoy mostrando la maquina ya atacada, pero si ustedes quieren ver este log pueden hacer un intento fallido de inicio de sesion para que este aparezca.</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/4584e0f0-383a-4e34-af15-2254d328db09)

![image](https://github.com/user-attachments/assets/562053e9-2a4a-47b3-b4e1-5d542d7e6a68)



<br />
<br />
<p align="center">




<br />
<br />
<p align="center">
<b>Para asegurarnos de que cualquiera en internet pueda descubrir nuestra VM facilmente, necesitamos desactivar el Firewall de WindowsTo make sure that everyone on the internet can discover my VM I need to disable the windows firewall. </b> <br/>
</p>

![image](https://github.com/user-attachments/assets/1a662d87-7e05-4562-9356-73fb741708ca)
![image](https://github.com/user-attachments/assets/75d604b9-17a2-4a08-8bdb-4731a28b7aec)


<br />
<br />
<p align="center">
<b>Ahora que nuestra VM esta expuesta al internet podemos empezar configurando el script de Powershell, este script va a parsear Event Viewer especificamente buscando por el EventID 4625. Luego va a enviar la direccion IP desde los Failed Logon Attemps hacia la pagina de IPgeolocation a traves de una API. El script va a recibir toda la data geográfica y la va a guardar como una cadena de texto (string) en un archivo de log llamado failed_rdp.log, a futuro vamos a usar este archivo para mapear los ataques en Microsoft Sentinel</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/776225ba-59ab-40d7-a30a-df495b415e73)



<br />
<br />
<p align="center">
<b>Una vez abierto el Powershell pegamos el script que lo pueden encontar subido en el github como Powershell Script, luego guardamos en script en el escritorio como Log_Exporter. Llegado a este punto ya deberian crearse una cuenta en IPgeolocation para que les otorgue la API key que vamos a estar usando en el script.</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/b812dd89-3f99-4991-9234-53b7b9033306)
![image](https://github.com/user-attachments/assets/8bf0a4c2-39a7-4981-b8d6-d355a714b71d)
![image](https://github.com/user-attachments/assets/bfb2d5cb-2ec2-439f-a1a0-3773e0722338)




<br />
<br />
<p align="center">
<b>Ejecutamos el script con la tecla  F5, habiendo ya ingresado nuestra API key donde se encuentran las comillas vacias($API_KEY      = "").La salida que ven del script es como se ve cuando alguien falló un intento de inicio de sesion.A partir de ahora deben esperar a que la maquina sea tacada para que se empiecen a generar los logs</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/b37c0e44-0ddb-4df6-97a9-b05f2a0ae3a0)

<br />
<br />
<p align="center">
<b</b>Las siguientes imagenes muestran como la data es guardada en el archivo de log failed_rdp en formato de cadena de texto (string).   <br/>
</p>

![image](https://github.com/user-attachments/assets/89c8da17-eb52-4171-bbe3-23fac6be4a9b)
![image](https://github.com/user-attachments/assets/1ffcd239-75db-4f12-94be-785d363661be)
![image](https://github.com/user-attachments/assets/394badac-c746-4c1b-9678-078c0f11ee49)






<br />
<br />
<p align="center">
<b>Una vez corroborado que el script funciona correctamente, podemos dirigirnos al Log Analytics Workbooks para crear un log personalizado para traer los failed_rdp logs dentro del Log Analytics de Azure.Lo que tenemos que hacer ahora es copiar el contenido de nuestro archivo failed_rdp que está en nuestra vm,  luego crear el mismo archivo en nuestra maquina Host, y pegar el contenido  para poder seleccionarlo como sample log.</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/6f9fc0b2-5f2c-4780-b75f-2f7c8e8abfe7)
![image](https://github.com/user-attachments/assets/055d2059-5567-4232-b4e7-4bb0c5b9f6df)
![image](https://github.com/user-attachments/assets/cfa35f9e-5b3d-45b2-8b43-473894985f12)
![image](https://github.com/user-attachments/assets/3c15bdbd-b791-42ee-8728-2cb246afda46)
![image](https://github.com/user-attachments/assets/f93e5d26-5d76-471f-9978-727dcf21ca92)
![image](https://github.com/user-attachments/assets/49e7aa92-b3f8-4d37-8119-75e72ac5ccf5)
![image](https://github.com/user-attachments/assets/c14d1f86-3908-4a7c-9ed3-4cd3d576be26)
![image](https://github.com/user-attachments/assets/4151d54c-cce2-4519-89ae-c2439a674322)

<br />
<br />
<p align="center">
<b>En esta sección pregunta por la ruta de recopilación que es donde los logs se encuentran en nuestra VM, la ruta es C:\ProgramData\failed_rdp.log. Luego tenemos que nombrar nuestra log personalizado , pueden nombrarlo como quieran, en este caso usé FAILED_RDP_WITH_GEO y por ultimo lo creamos. </b> <br/>
</p>

![image](https://github.com/user-attachments/assets/e3b6ab66-99c0-4d2c-98a9-0b16596c3955)
![image](https://github.com/user-attachments/assets/6fe8558f-c772-474c-ae60-69f0afc3ea82)








<br />
<br />
<p align="center">
<b>La creacion del log personalizado va a ser instantanea, pero la sincronización de la data de la VM hacia el Log Analytics va a tardar 
 un tiempo. Pueden ir probando el siguiente query para ver si ya esta sincronizado: FAILED_RDP_WITH_GEO.</b><br/>
</p>

![image](https://github.com/user-attachments/assets/bbe71a18-4310-4a58-b896-7d587be78987)
![image](https://github.com/user-attachments/assets/4c594994-292f-4b0e-b873-79a1e9308c96)
![image](https://github.com/user-attachments/assets/269c98f8-2c12-42c2-9e69-e010b24f847a)




<br />
<br />
<p align="center">
<b>Lo último que queda por hacer es la creación del mapa, para esto necesito crear un nuevo Workbook en Microsoft Sentinel. Al crear uno ya nos muestra widgets y gráficos que estan predeterminados, los eliminamos para poder empezar a configurar nuestro query que lo que va a hacer es consultar la data y los campos del Log Analytics, basicamente le estamos mostrando a Microsoft Sentinel el conjunto de data que tiene que usar.El query está subido en el github como Workbook Query</b><br/>
</p>

![image](https://github.com/user-attachments/assets/f3898907-ecc4-4728-a278-6dc244689f3f)
![image](https://github.com/user-attachments/assets/56dd7c21-69c2-42a4-ac9f-eae9b504be40)
![image](https://github.com/user-attachments/assets/70e116ba-9945-4513-ac66-959fb35dda41)
![image](https://github.com/user-attachments/assets/c8ce8c79-b727-4062-bd5c-5093bbaa5b01)
![image](https://github.com/user-attachments/assets/340f1666-cbf5-47e7-afe6-4497f8134e47)
![image](https://github.com/user-attachments/assets/ee45c2cf-eee2-47c6-a3aa-9599b9b3d226)












<br />
<br />
<p align="center">
<b>Luego de creación del query solo queda elegir como quiero que se visualicen estos datos, en este caso vamos a elegir mapa y lo configuramos de la siguiente manera:.</b>  <br/>
</p>

![image](https://github.com/user-attachments/assets/67d6abac-77d1-4fc2-8dd8-cdcdc6c8d392)
![image](https://github.com/user-attachments/assets/2986d47c-be7c-4185-ac91-381c2494795d)
![image](https://github.com/user-attachments/assets/d5db7e7e-62b0-4568-b8df-993d50d9ecfa)
![image](https://github.com/user-attachments/assets/54a57d85-6bb8-4256-b873-e62c10da5bf3)
![image](https://github.com/user-attachments/assets/69db7d0a-cc78-4a23-ae98-3ce074b2f1be)



<br />
<br />
<p align="center">
<b>En esta ultima imagen muestro el resultado final de este proyecto, en el transcurso de pocos dias recibí mas de 3000 ataques de diferentes partes del mundo, como se puede apreciar en el mapa. El lugar principal donde mas recibí estos intentos de login fallidos es Brasil con mas  2000 de intentos y Rusia con casi 1000 intentos, pero se pueden ver ataques en todas partes del mundo,como Estados Unidos desde diferentes estados, o China e India. </b>  <br/>
</p>

![image](https://github.com/user-attachments/assets/c5bb9e0e-8196-45ce-acf8-06b771f4bd93)





