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
<b>From here I can log into my VM via Remote Desktop. I open up Remote Desktop on my native PC, enter the public IP address and credentials needed and connect in! I configure the HoneyPot a bit.</b> <br/>
</p>

![Connect_With_RDP](https://user-images.githubusercontent.com/108043108/225381141-3514bcc6-3417-4281-ad67-1b4af20edfd5.JPG)
![Logging_Into_VM_Via_RDP](https://user-images.githubusercontent.com/108043108/225381164-d6591c73-4cf0-4fee-85c9-22d56abfc8ab.JPG)
![Logging_Into_VM_via_RDP_2](https://user-images.githubusercontent.com/108043108/225662102-2a73273a-c0f7-4424-833f-2d981f5859e0.JPG)

<br />
<br />
<p align="center">
<b>After I'm successfully logged into the VM via RDP, I navigate to Event Viewer. Event Viewer logs everything that goes on in a windows system. It gives each action an EventID so it can be more easily navigated or browsed by the EventID. For this project we are concerned specifically with EventID 4625, which is Failed Logon Attempts. These logs can be found in the Security tab. In the pictures below, I run another instance of Remote Desktop and try to log into the VM with the wrong password. This creates a failed logon attempt which is then logged and recorded in Event Viewer.</b> <br/>
</p>

![Event_Viewer](https://user-images.githubusercontent.com/108043108/225381724-2603d72a-5334-479d-b555-c1d0baaeb875.JPG)
![Event_Viewer_2](https://user-images.githubusercontent.com/108043108/225381737-0bc2d129-b045-4099-a5fe-d34b4d43f673.JPG)


<br />
<br />
<p align="center">




<br />
<br />
<p align="center">
<b>To make sure that everyone on the internet can discover my VM I need to disable the windows firewall. I do this by going into the windows search bar and searching wf.msc. Once inside the windows firewall, I begin disabling everything. I then open up CMD in my native computer and try to ping the VM again. This time it receives replies from the VM because the firewall is no longer blocking ICMP requests. </b> <br/>
</p>

![Turn_Off_Firewall](https://user-images.githubusercontent.com/108043108/225385096-6cdeaa2a-3411-46fa-bad1-0afcee031860.JPG)
![Turn_Off_Firewall_2](https://user-images.githubusercontent.com/108043108/225385112-d4ada032-3cdc-4650-b60a-0a373cddfd17.JPG)

<br />
<br />
<p align="center">
<b>Now that my VM is exposed to the internet I can begin the setup of my PowerShell script. It is the heart of this project. This PowerShell script will parse Event Viewer specifically looking for EventID 4625. It will then send the IP address from the failed logon attempts to the website IPgeolocation.io via an API. The reason I did this is because the IP address in event viewer does not contain any geographical location. It was easier to send the data to a system dedicated to pulling that information out and sending it back to myself rather than building it from scratch. The PowerShell script will then receive all that geographical data and save it as a string in a logfile named failed_rdp.log. I will use this logfile later on in the project to be able to map the attacks live in Microsoft Sentinel.</b> <br/>
</p>

![Create_PS_Script](https://user-images.githubusercontent.com/108043108/225409638-0f9735a1-eca0-446f-afb7-74ef0d427ebf.JPG)


<br />
<br />
<p align="center">
<b>After I open PowerShell, I paste my script that was written before the start of this project. I then save that script to the desktop as Log_Exporter. At this point you should go and make a profile on IpGeolocation to get your API. You'll paste your own API into the PS script. You'll get 1000 free calls, but they can go fairly quickly, so I recommend going back into the Windows Firewall settings and turning them back ON until you're done setting up your Log Analytics Workbooks Custom Fields later on in the lab. After those are configured, you can then turn off the Firewall.</b> <br/>
</p>

![Create_PS_Script_2](https://user-images.githubusercontent.com/108043108/225410802-01a83b34-e79a-4516-8bdb-70c01baa76d7.JPG)
![Create_PS_Script_3](https://user-images.githubusercontent.com/108043108/225410821-7078f7c1-1928-49c4-8e03-a123ef43cb3f.JPG)

<br />
<br />
<p align="center">
<b>I run my Log_Exporter script. So that it was easier to read I made it so that the script outputs in pink and black. The API_KEY you see has been changed after I finished the project. In the first photo you can see that my script is working just fine. The output you see is the first failed login that I did earlier. The second photo shows how the data is saved into the failed_rdp logfile in string format. I included some sample data in this file because later it will be needed to train the AI in Log Analytics Workbooks and Microsoft Sentinel. More data equals more precision.</b> <br/>
</p>

![Run_PS_Script](https://user-images.githubusercontent.com/108043108/225412357-ee390c08-c131-4658-b6d0-b2c6f0485850.JPG)
![Training_Sentinel](https://user-images.githubusercontent.com/108043108/225412372-30c65d96-6678-4ac1-a3a9-d0f3d8331342.JPG)

<br />
<br />
<p align="center">
<b>At this point to test if the PowerShell script is working, I failed another logon attempt. As you can see someone has already found my VM and started to try and brute force it. This person was in Tunisia. They found it so fast, it was a bit annoying. I could have blocked his IP or enabled the firewall again until I was finished completely with my setup, but this data was perfect to train the AI in Azure, so I let it go at the time. In hindsight it was a bad move. The free API from IPgeolocation.io only allowed for 1000 calls. This person in Tunisia hit that limit very quickly, ruining my project. I had to pay $15 for an extra 150k API calls to save my project. </b> <br/>
</p>

![Showing_PS_Script_Someone_Already_Found_VM](https://user-images.githubusercontent.com/108043108/225413356-343d9ae2-240d-4841-b41f-62cff77e51ab.JPG)

https://user-images.githubusercontent.com/108043108/225419861-a5c9bce1-3f6d-42e9-9bd4-dcd7ca816a33.mp4

<br />
<br />
<p align="center">
<b>Now that I know my PowerShell script is working as it should, I head over to Log Analytics Workbooks to create a custom log so that I can bring my failed_rdp log into Log Analytics. In Log Analytics I navigate to my VM and create a Legacy Custom Log. It asks for a sample log, which is inside the VM. I can't download the logfile from the VM to my native computer, so I have to open the logfile inside the VM, copy the contents, go back to my native computer and open Notepad, paste the copied contents in and save the file to my desktop. From there I can import it into Log Analytic Workbooks. This sample data will be used to train Log Analytics.</b> <br/>
</p>

![Custom_Logs](https://user-images.githubusercontent.com/108043108/225421446-5a0c5c4f-62a0-4a07-bb6a-b36edd43b7fd.JPG)
![Custom_Log_2](https://user-images.githubusercontent.com/108043108/225421932-a4d520b3-5f9a-4851-badb-e07744735144.JPG)
![Custom_Log_3](https://user-images.githubusercontent.com/108043108/225421947-dcaadf65-e212-41ae-8e83-322a310df6f7.JPG)
![Custom_Log_4](https://user-images.githubusercontent.com/108043108/225421956-8dd57a76-b0b0-44a0-8f10-675ba6b1dfa9.JPG)
![Custom_Log_5](https://user-images.githubusercontent.com/108043108/225421995-664b1d05-5ba7-48a3-9e90-a1c5eedeed36.JPG)

<br />
<br />
<p align="center">
<b>Next it asks for the collection path. The collection path is where the log lives in the VM, so it asks for a path that Log Analytics can take to reach that logfile. The path to that file is C:\ProgramData\failed_rdp.log. If this path is wrong, Log Analytics wouldn't be able to collect the log information. Next, we have to name our custom log. I decided to name it FAILED_RDP_WITH_GEO and the .CL (Custom Log) will automatically be appended to it. When querying the database later this will basically be the name of the table. We then create the custom log. </b> <br/>
</p>

![Custom_Log_6](https://user-images.githubusercontent.com/108043108/225422804-46df9880-9734-420c-84a9-e18ec3a68b57.JPG)
![Custom_Log_7](https://user-images.githubusercontent.com/108043108/225423680-580feffc-72e3-4eaf-9fd0-57d0376976b3.JPG)
![Custom_Log_8](https://user-images.githubusercontent.com/108043108/225424273-b392d383-4025-4206-b406-0fd79058d263.JPG)

<br />
<br />
<p align="center">
<b>While that is being provisioned, the creation will be instant, but the data won't be synced from the VM to Log Analytics for a while. I decided to query the Event Viewer, which should have already been synced. You can see in picture 1 that it is indeed showing all the logs. After a little while I decided to query the newly created FAILED_RDP_WITH_GEO custom log, and it is indeed showing information meaning that the VM and Log Analytics as synced and is sending/receiving data. </b><br/>
</p>

![Security_Event_4625](https://user-images.githubusercontent.com/108043108/225425000-75d4b1ae-fa60-48a4-af30-8fc5ab52cb8f.JPG)
![Failed_RDP_LOGS](https://user-images.githubusercontent.com/108043108/225425211-162958fe-13cb-455a-99bf-2b24897a5a29.JPG)

<br />
<br />
<p align="center">
<b>Now I have to go in and extract the fields my log uses. This will allow me to later use those fields in Microsoft Sentinel. I right-click a failed rdp login log that has all the raw data in it from my PowerShell script and highlight the data I want. I then name the field that data is going to be in. Once that extraction happens, the Log Analytics AI looks at all of my other sample data and actual logs that were generated and sees if it can pull the correct data. This part is where I have to go in and correct any errors the AI has by again highlighting the correct data point it needs to look for. To do this, I have to scroll through the list (higlighted in blue in picture 4), right click any that is wrong and re-highlight the correct information I want it to pull. The custom fields you're going to create at this stage is: latitude, longitude, destinationhost, username, sourcehost, state, country, label, timestamp.</b><br/>
</p>

![Extract_Fields](https://user-images.githubusercontent.com/108043108/225436517-0a82f48e-82c8-45cf-b351-634ee8ba41c7.JPG)
![extract_data_2](https://user-images.githubusercontent.com/108043108/225436525-d57e194f-8a55-4e9a-915b-9d8a413a83e4.JPG)
![extract_data_3](https://user-images.githubusercontent.com/108043108/225436533-e68158a6-3cd5-4f84-a6b2-1329a1339016.JPG)
![extract_data_4](https://user-images.githubusercontent.com/108043108/225436540-4ec75b85-8076-493e-9fb7-8c5bc24cba4b.JPG)


<br />
<br />
<p align="center">
<b>I waited a little while to see if the fields would populate properly. They all do except sourcehost.CL and I couldn't figure out why. I deleted that field and extracted it multiple times but no matter what I did it would not populate. I could not use an unpopulated field in my sentinel live map, so in the end I decided to delete it and not use that data point at all.</b>  <br/>
</p>

![Sourcehost_wouldnt_update](https://user-images.githubusercontent.com/108043108/225451005-edabe896-94b1-4d11-8853-42e4a4ce8e83.JPG)
![sourcehost_wouldnt_update_2](https://user-images.githubusercontent.com/108043108/225451011-80b1dbad-2e90-4407-bfa5-5c516b66a991.JPG)

<br />
<br />
<p align="center">
<b>The next step taken was to begin setting up my geomap that will pinpoint and map out where the attacks, or login attempts were coming from. I do this by navigating to Microsoft Sentinel. In the first picture we can see that the SIEM has been collecting data properly and categorizing it. I did not set any alerts for this project but it was certainly possible, maybe for a future video. We can see in this picture that there are nearly 10k events and 6.9k security events, coming from Event Viewer in the VM with 2.3k failed RDP attempts. I haven't even finished setting up this project but the person from Tunisia was hard at work trying to brute force into my VM. Good luck ha ha ha!</b>  <br/>
</p>

![Setting_Geomap](https://user-images.githubusercontent.com/108043108/225451297-e0c57af2-5333-4b6f-8fff-79b11b816e77.JPG)

<br />
<br />
<p align="center">
<b>Moving on. To create the map, I want I'll need to create a new workbook in Sentinel. After clicking into workbooks, there is some default graphs or widgets in there. I want to delete those. After deleting those I then get started on creating a new workbook.</b>  <br/>
</p>

![setting_geomap_2](https://user-images.githubusercontent.com/108043108/225452457-2c805584-bee1-4c4b-adb3-aa73726c0d0f.JPG)
![setting_geomap_3](https://user-images.githubusercontent.com/108043108/225452473-99fd3ef3-04ff-49e6-aecf-94793b7e60f2.JPG)
![setting_geomap_4](https://user-images.githubusercontent.com/108043108/225452571-ef8a7a55-a541-44ba-9269-24a32d56d14c.JPG)

<br />
<br />
<p align="center">
<b>To create the map, I need to add a query. Remember, I need to query the data and the fields from Log Analytics. Basically I'm pointing out the dataset I want Microsoft Sentinel to use. In the query I tell it to specifically exclude (!=) the data points that include "Samplehost" since those aren't real attacks and I don't want them to populate on the map.</b>  <br/>
</p>

![setting_geomap_5](https://user-images.githubusercontent.com/108043108/225453225-24b50358-ac8c-4897-9e44-8d21f4075239.JPG)

<br />
<br />
<p align="center">
<b>After I queried the data points I want, I now have to choose how I want to express/visualize them. In this case I want them visualized as a map! I choose the map setting and then configure the map to plot the attacks by latitude/longitude. I could do it by country but some of the attacks coming through was not including the country. I changed the metric settings to make the bubbles bigger using the event counts. The more events the bigger the bubble. I apply the settings and my map is done!</b>  <br/>
</p>

![setting_geomap_6](https://user-images.githubusercontent.com/108043108/225453707-62496864-0c33-4d3d-988c-3260b07e9c9b.JPG)
![setting_geomap_7](https://user-images.githubusercontent.com/108043108/225454149-b35a2412-574f-474a-acca-3b541148208a.JPG)
![setting_geomap_8](https://user-images.githubusercontent.com/108043108/225454159-593b96e9-e6dc-4cbc-b6fa-bcb955495bfd.JPG)


<br />
<br />
<p align="center">
<b>After a few hours and right before I decided to stop the project, you can see that there was a total of 10,529 attacks or failed login attempts. 20 were in the USA, which was me testing the PowerShell script, 9 from Cambodia, and a whopping 10.5k from Tunisia. They were using automated brute-forcing software to try thousands of different password combinations and usernames. There were even more attempts, but my PowerShell script had to be stopped and started multiple times. This is why it's important to use strong passwords and uncommon usernames! The second picture is the number of API calls I had that day. All of them are not shown because I had to upgrade the number of calls I could make. </b>  <br/>
</p>

![Stopped_Because_of_API_Calls_Limit_At_150k](https://user-images.githubusercontent.com/108043108/225454550-20b2f3d6-5593-44cf-8bbc-290fb941da8c.JPG)
![API_requests](https://user-images.githubusercontent.com/108043108/225455054-0576753d-b3d0-40dd-aa4b-70246b1616e8.JPG)

<br />
<br />
<p align="center">
<b>After I was done, I had to delete the resource group I created for this project. If I left it alone, it would eat up my $200 credit I would need for future projects. The reason I put everything under one resource group is for this exact reason, easier deletion of the resources I created. Now nothing would be left behind.</b>  <br/>
</p>

![Deleting_Resources](https://user-images.githubusercontent.com/108043108/225455713-94e78229-b9ec-4f2e-ae17-24ed2636f7a1.jpg)
![deleting_resources_2](https://user-images.githubusercontent.com/108043108/225455718-29980ca4-9976-4abf-955f-573f6d55fdd9.JPG)

<br />
<br />
<h2 align="center">Decided To Run The Lab Again.</h2>
</p>

<br />
<br />
<p align="center">
<b>I decided to do the lab again. I wanted to give attackers more time to attack the exposed VM so more locations could be represented on the world map. My decision paid off. I’m very happy that this time a more diverse group of threat actors decided to attack my VM, which resulted in this version of the world map.</b>  <br/>
</p>

![worldmap2](https://user-images.githubusercontent.com/108043108/226672121-d80091a0-6f7b-4f1d-b5bd-d9416823c6bf.JPG)

<br />
<br />
<p align="center">
<b>I noticed an issue while running this lab a second time. Periodically my PowerShell script would stop updating. It was still running, but no new entries would be generated for 5 – 15 minutes. I thought that was strange. I knew for a fact there were many different countries attempting to brute-force their way into the VM, so there should be a steady stream of new entries. Especially because I set the refresh rate in this PS script to 300 Milliseconds, down from 1 second in the earlier lab. I decided to investigate what was happening. The first step in my investigation was to compare the number of security events where Event ID = 4625 against the number of Failed_RDP_Custom_Log’s generated. I accomplish this by writing queries in Log Analytics Workbook.</b>  <br/>
</p>

![Security_Event_ID](https://user-images.githubusercontent.com/108043108/226673169-7b2f64a3-7ab1-4918-98e8-b680a553aabf.jpg)
<p align="center"><i>After writing my query we can see here that there was a total of 9282 EventID 4625 Security Events. This means that threat actors attempted to log into my VM 9282 times.</i></p>  <br/>

![Failed_RDP_Log](https://user-images.githubusercontent.com/108043108/226673194-787f3452-309d-4d0b-a375-c653fb16c4b1.JPG)
<p align="center">
<i>After writing this query we can see it returned a total of 3892 records, which means that my Custom Log only captured 3892 login attempts. Quite a bit away from 9282.</i>
</p>

<br />
<br />
<p align="center">
<b>Okay, there is a discrepancy here. Both of those number of records should match, or at least be very close to each other. Next I decided to look at my API usage for today. It shows 3541 API requests. It's much closer to my Custom Log FAILED_RDP_CUSTOM_LOG. So now I know it isn't my VM that is failing to send the data to my custom log.</b>  <br/>
</p>

![API](https://user-images.githubusercontent.com/108043108/226676332-d906603b-59c1-4ad0-94c0-fb665bb26a73.JPG)

<br />
<br />
<p align="center">
<b>Lastly, I chose to go into Microsoft Sentinel and see what the dashboard is showing. My PowerShell Script was still running in the background so the numbers aren't an exact match because these screenshots were taken a few minutes apart. Again, it is showing a number (4,100) that is much closer to the FAILED_RDP_CUSTOM_LOG query.</b>  <br/>
</p>

![Sentinel](https://user-images.githubusercontent.com/108043108/226676717-a08eb2c2-6ae1-46f7-8d78-7359b2d610bf.JPG)

<br />
<br />
<h2 align="center">Conclusions</h2>

<br />
<br />
<p align="center">
<b>What I suspect is happening is that the Windows Event Viewer is working just fine. It is properly logging ALL login attempts and is showing the actual number of attempts that has happened. The weak link in this problem is the PowerShell script. The reason my PowerShell script was pausing for extended amounts of time is because it was being overwhelmed. I set the refresh rate to 300 Milliseconds, but the actual login attempts from several different attackers was much more than 1 attempt per 300 Milliseconds. My PowerShell script was a bottleneck. If it could record each attempt as it happened, it would show the actual number of attempts and be in line with Event Viewer. It could only record 1 attempt per 300 Milliseconds, so some login attempts were being lost or backlogged (I doubt it was backlogged), which lead to the discrepancy we are seeing. Perhaps in the future I could lower the refresh rate even more to allow more login attempts to go through.</b>  <br/>
</p>


<br />
<br />

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
