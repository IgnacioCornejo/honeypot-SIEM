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


![image](https://github.com/user-attachments/assets/8aca1c73-ad54-4788-aa44-65be8d5020ab)
![image](https://github.com/user-attachments/assets/bf69d529-56d9-4816-bf08-42088463fa14)
![image](https://github.com/user-attachments/assets/548f9ca2-ffe3-47b4-b964-c779844c47f8)
![image](https://github.com/user-attachments/assets/624fd2bd-abb0-4b75-896a-1018cfbf4803)









<br />
<br />
<p align="center">
<b>Lo siguiente que voy a hacer es  crear la máquina virtual que va a actuar de honeypot. A la hora de elegir el grupo de recurso, seleccionen el que crearon recientemente.</p>

![image](https://github.com/user-attachments/assets/47c75c95-24c1-4bbd-8b6d-42d006901019)
![image](https://github.com/user-attachments/assets/7eb8ef84-2606-4cf0-93d3-a4e7a642c189)
![image](https://github.com/user-attachments/assets/75099603-f2ef-472f-a8c4-dc3f88273385)





<p align="center"> Recomiendo elegir 2 vcpu, que es lo suficiente que la maquina necesita para no crashear.</p>

![image](https://github.com/user-attachments/assets/2572e905-e834-487e-9a3e-bbd9572d387e)


 <p align="center">Cuando vayan a crear el usario y contraseña de la maquina virtual no lo olviden porque con esta informacion vamos a entrar de manera remota a la maquina posteriormente.</p>

![image](https://github.com/user-attachments/assets/81aa8bb1-9ab9-4c78-9a0e-ca4c8a1ee1f3)


<p align="center"> En esta seccion seleccionen proximo hasta llegar al apartado de networking.</p>

![image](https://github.com/user-attachments/assets/e3f0293a-678c-4eda-bc46-9b1878f4b389)



 <p align="center">El proximo paso es crear y configurar un nuevo Network Security Group (NSG) que vendria a actuar como nuestro firewall. En este caso no queremos ninguna regla que limite y bloquee tráfico ya que queremos permitir que cualquiera se pueda comunicar con nuestra máquina, La configuracion de la nueva regla que creamos es para que permita cualquier puerto (*)</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/0d32a693-6d88-4912-b616-f2f2b6247873)
![image](https://github.com/user-attachments/assets/2cb9f342-77f5-437a-b8ab-7a85f0e0beb3)
![image](https://github.com/user-attachments/assets/ec8c5b4f-d33f-4f9f-87c6-42e8dc7f2087)
![image](https://github.com/user-attachments/assets/54dc3215-909d-4830-9136-2ee8b2778a6a)
![image](https://github.com/user-attachments/assets/4e6b8c1b-89dd-41bf-b733-1abf12451ddb)
![image](https://github.com/user-attachments/assets/a002f906-ae5c-49f1-bfe7-2bf5ecb8e481)




<br />
<br />

<p align="center">
<b>Mientras se crea la máquina virtual,podemos empezar creando nuestro Log Analytics Workspace. Recuerden elegir siempre el grupo de recursos creado para este lab  .</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/70067681-d938-4d77-a2e5-f7c98231c18c)
![image](https://github.com/user-attachments/assets/3413fd23-551d-43bd-80d4-fb1a42ea1a88)
![image](https://github.com/user-attachments/assets/66f003bf-0d43-421b-acb2-4af4e975ccdb)
![image](https://github.com/user-attachments/assets/374b6720-e6fa-4028-8806-91c88a6b4a14)





<br />
<br />
<p align="center">
<b>Ahora nos dirigimos a Microsoft Defender for Cloud para acceder a un plan que nos permita tomar y agregar data para nuestro SIEM, Microsoft Sentinel, para usar luego.
</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/31cf453c-4702-4a51-af5d-9c0f06fb1489)
![image](https://github.com/user-attachments/assets/dc5bf363-ad80-4a8d-945a-cdc808b0c8ad)
![image](https://github.com/user-attachments/assets/48b80efd-f45a-41e2-9280-3813daa2b398)
![image](https://github.com/user-attachments/assets/68528d9b-4c24-44a6-82f7-4f0502ccde09)













<br />
<p align="center">
<b>Una vez ya creada la maquina virtual, podemos ir al Log Analytics Workspaces y conectar nuestra VM al servicio  .</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/ad68244d-f4b9-4c07-99f3-d9024f7742b6)
![image](https://github.com/user-attachments/assets/cdea5f61-dc98-4442-b56e-751427dea07f)
![image](https://github.com/user-attachments/assets/269b661c-3b74-4a44-892b-533d64d9e752)





<br />
<br />
<p align="center">
<b>Ahora debemos crear una instancia de Microsoft Sentinel y conectarla a nuestra VM. En mi caso la VM no aparece porque ya la conecte previamente, pero a ustedes les va a salir para seleccionar y luego la agregan </b> <br/>
</p>

![image](https://github.com/user-attachments/assets/e3a52d5f-6913-43c4-ba6b-61cc4f256505)


<br />
<br />
<p align="center">
<b>Ahora que ya esta todo configurado en el dashboard de Azure, podemos entrar a la vm y configurarla, para eso necesitamos obtener la direccion IP para entrar via Escritorio Remoto.</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/0a8dee6a-3fa8-4125-902b-1a9754784c79)



<br />
<br />
<p align="center">
<b>Abrimos el escritorio remoto e ingresamos las credenciales que creamos cuando hicimos la VM .</b> <br/>
</p>

![image](https://github.com/user-attachments/assets/a7d0a434-9a71-4f04-9676-6a4cb62bd491)


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
<b>Para asegurarnos de que cualquiera en internet pueda descubrir nuestra VM facilmente, necesitamos desactivar el Firewall de Windows. </b> <br/>
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
![image](https://github.com/user-attachments/assets/d1e5899d-07d9-4bfe-afff-7d0b6653d688)





<br />
<br />
<p align="center">
<b>Ejecutamos el script con la tecla  F5, habiendo ya ingresado nuestra API key donde se encuentran las comillas vacias($API_KEY      = "").La salida que ven del script es como se ve cuando alguien falló un intento de inicio de sesion.A partir de ahora deben esperar a que la maquina sea atacada para que se empiecen a generar los logs</b> <br/>
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
![image](https://github.com/user-attachments/assets/01465d01-be8e-4b22-8049-01c7d49a1a80)
![image](https://github.com/user-attachments/assets/4f96d696-db21-4785-a8e7-21b52353b6dc)
![image](https://github.com/user-attachments/assets/451394bd-71e0-439f-ac11-b6d0ea9701cc)
![image](https://github.com/user-attachments/assets/b15d30be-8a53-4181-b786-6dae0d4d57aa)
![image](https://github.com/user-attachments/assets/695b8042-02b9-4073-aa82-29c638a322cc)
![image](https://github.com/user-attachments/assets/e569090d-fc3d-4aae-a2a1-9b914217a322)
![image](https://github.com/user-attachments/assets/8759cc5a-5d1b-49b3-a4b4-375cf28dfe47)

<br />
<br />
<p align="center">
<b>En esta sección pregunta por la ruta de recopilación que es donde los logs se encuentran en nuestra VM, la ruta es C:\ProgramData\failed_rdp.log. Luego tenemos que nombrar nuestra log personalizado , pueden nombrarlo como quieran, en este caso usé FAILED_RDP_WITH_GEO y por ultimo lo creamos. </b> <br/>
</p>

![image](https://github.com/user-attachments/assets/c360bbdc-970e-48c8-9a6b-5bb949d38cad)
![image](https://github.com/user-attachments/assets/48190161-124c-4753-8a24-a5557e7e4c67)









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





