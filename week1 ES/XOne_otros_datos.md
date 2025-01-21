# Otros datos relevantes que incluyen ejemplos

## URL IMPORTANTES
Para obtener datos de la base de datos del servidor, se hace un post y en el body se pone la query a ejecutar.  
http://marinabcn.byxone.com/xonejsonsql/default.aspx  
Se puede crear una página con formularios para gestionar los datos.  
http://marinabcn.byxone.com/xonesupport/  
Para gestionar dispositivos, acceder a la base de datos, hacer cambios en los datos y meterlos en la réplica.  
http://marinabcn.byxone.com/xonemanager/  
Wiki donde hay documentación sobre cómo programar, código de ejemplo, documentación.  
http://www.xoneisp.com/xonewiki/  

## INSTALACIÓN DE LA APP
C:\XOneRepository\Installations\00000281\Android\install

## ENVIAR SMS AL TELÉFONO
SMS para solicitar que el dispositivo envíe la base de datos al servidor : ##X-SENDBD##nombre-de-la-base-de-datos##  
SMS para solicitar que el dispositivo restaure el LiveUpdate: ##X-RESTARTLU##  
 
## DONDE RECOGER DATOS Y LOGS ENVIADOS POR EL DISPOSITIVO
http://xoneisp.com/logcat/ 
 
## DECODIFICAR DATOS EN BASE64
https://www.base64decode.org/ 
 
## PARÁMETROS PARA LA INSTALACIÓN DE LOS SERVICIOS MONITOR  E ITF
Los nombre van en función de lo que se instale  
installutil /ServiceName=XOneMonitor /DisplayName="XOne Monitor" /i XOneMonitorServer.exe

## ERRORES AL CONECTAR POR ESCRITORIO REMOTO

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\CredSSP\Parameters AllowEncryptionOracle como REG_DWORD valor 2 (hexadecimal)  
El problema es del Cliente de escritorio remoto del XP que no soporta network level authentication por defecto. Hay que modificarle un par de entradas en el registro del equipo XP que se desee conectar a un Windows Server 2012 y luego reiniciar.  
Las claves son:  
 
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders  
Modificar el REG_SZ llamado "SecurityProviders" y adicionarle a la lista separada por comas una coma y después el valor: credssp.dll  
 
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa  
Modificar el REG_MULTI_SZ llamado "Security Packages" y adicionarle al final de la lista el valor: tspkg  
 
Luego reiniciar el equipo y ya debe poder abrir un escritorio remoto a un servidor 2012  

Error de autenticación. No se permite la función solicitada.
Ejecutamos gpedit.msc,  
En la carpeta Plantillas administrativas/Sistema/Delegación de credenciales  
Elemento Corrección del Oráculo de cifrado, damos doble click, lo habilitamos y poner nivel de protección Vulnerable.  
 
## MUESTRA LA QUERY QUE LANZA

if(index == 2)ui.showToast(self.getContents(contentName).getDevelopedAccessString()); 
// compartir contenido
ui.shareData("Subject",self.getContents(contentName).getDevelopedAccessString(),"");

## EJEMPLO DE CÓDIGO (Login)
En la coll de Login-coll, le quitas la forma de hacer el login actual, y pones este script en el botón aceptar o entrar:
```
function doLoginNew() {
    
    var menu="";
    //En este IF, tu pones tu función que sea, para decidir que cargue un menu u otro. Es decir, según el tipo de usuario o lo que sea, pues vas a un lugar u otro.
    if (definirMenuEntrada().toString()=="1")
        menu="MenuPrincipalFase0";
    else
        menu="MenuPrincipalFase1";
    
    //ui.showToast(definirMenuEntrada());    
    
    //ui.showToast(menu);
    
    var params = {
        userName: "admin",
        password: "",
        entryPoint: menu,
        // Opcional
        onLoginSuccessful: function() {
            //ui.showToast("Login OK!");
        },
        // Opcional
        onLoginFailed: function() {
            //ui.showToast("Login failed!");
        }
    };
    appData.login(params);
}
 
/**
 * Devuelve al login-coll, poniendo a null el usuario y empresa en appData.
 **/
function doLogout() {
    appData.logout();
}

// Ejemplo para salir de la aplicación
/**
 * Cierra la aplicación. Sustituto de failWithMessage con ##EXITAPP##
 **/
function exit() {
    appData.exit();
} 
```
Por Rubén Montero Silos
 
## DESCARGAR EL FRAMEWORK
https://xone.cloud/xoneinstall/index.html

## GESTION DE TABLAS CON Auth2

Para gestionar las tablas ya no se necesitan webservices, ahora se hacen post, con autorización Auth2:  
**Get Token url**: https://ids.xone.cloud/connect/token  
**Get token manual url**: https://web.xone.cloud/Home/Client  
**Action url**: https://web.xone.cloud/api/ITF/DoAction  

Header  
Content-Type: application/json  
Authorization: Bearer (token obtenido)  
 
En el body del POST  
{
  "coll": "Usuarios",     
  "action": "write",    //[select, write,  drop]
  "where": "ID>0",
  "data":{
"IDEMPRESA": 1,
  "LOGIN": "lituan",
  "PWD": null,
  "NOMBRE": "Lituan Carrillo",
  "USERTYPE": "MUSER"
  },
    "keys" : [
       "LOGIN","IDEMPRESA"
      ]
}
 
select, update, drop: Respetan el where.  
write: No respeta el where, si existe hace un update.  
select: No respeta el data.  

## EJEMPLOS DE CODIGO

//Uso de filtros  
```
coll.setFilter() : agrega un filtro a la coll, OJO.  
coll.setLinkFilter(): utilizar este que no altera el filtro.  
```
//Herramientas para debug en Javascript  
```
function doDebugTools(metodo){
    var result,message,urlLog;
    var debugTools = createObject("DebugTools");
    
    if(typeof debugTools !== "undefined"){
         // URL donde se enviarán los datos.
        urlLog = "http://www.xoneisp.com/XoneLogRec/reclog.aspx";
        
        switch (metodo) {
	// Pedir el log en Android, este será enviado al servidor
            case 0:
                message = "el log de android";
                result = debugTools.sendLog(urlLog);
                break;
	// Solicitar al dispositivo el envío de la base de datos al servidor
            case 1:
                message = "la base de datos";
                result = debugTools.sendDatabase(urlLog);
                break;
	// Solicitar al dispositivo el envío de la base de datos de depuración al servidor
            case 2:
                message = "la base de datos de depuracion";
                result = debugTools.sendReplicaDebugDatabase(urlLog);
                break;
	// Solicitar al dispositivo el envío de la base de datos de depuración de ficheros al servidor
            case 3:
                message = "la base de datos de depuracion de ficheros";
                result = debugTools.sendReplicaFilesDatabase(urlLog);
                break;
        }
        
        if( result == -1){
            ui.showToast("No se pudo enviar " + message.toString());
        }else{
            ui.showToast("Enviado correctamente.");
        }
    }else{
        ui.showToast("Funcion no implementada en IOS");
    }
    
    debugTools = null;
}
```
//Ejemplo muestra cómo obtener información del GPS.
```
function LlenarVarGPS() {
    var collGPS, objCollGPS;
    collGPS = appData.getCollection("ConectarGPS");
    try {
        objCollGPS = collGPS.getObject("LONGITUD","0");
        if (!objCollGPS) {
            //throw  "GPS no disponible. objCollGPS es: " + objCollGPS;
            return;
        }
        if (objCollGPS.STATUS != 1) {
            //throw "GPS no disponible. STATUS vale: " + objCollGPS.STATUS;
            return;
        }
        if (!objCollGPS.LONGITUD) {
            return;
        }
        var curEnt=appData.getCurrentEnterprise();
        curEnt.MAP_LASTLATITUDGPS=objCollGPS.LATITUD;
        curEnt.MAP_LASTLONGITUDGPS=objCollGPS.LONGITUD;
        curEnt.MAP_LASTRUMBOGPS=objCollGPS.RUMBO;
        curEnt.MAP_LASTALTURAGPS=objCollGPS.ALTITUD;
        curEnt.MAP_LASTVELOCIDADGPS=objCollGPS.VELOCIDAD;
        curEnt.MAP_LASTFECHAGPS=objCollGPS.FGPS;
        curEnt.MAP_LASTHORAGPS=objCollGPSHGPS;
        curEnt.MAP_LASTPRECISIONGPS=objCollGPS.PRECISION;
        curEnt.MAP_LASTPROVIDERGPS=objCollGPS.FUENTE;
    }
    catch (e) {
        //ui.msgBox(err,"Error",0);
    }
    finally {
    }      
}
```
//Ejemplo sobre cómo guardar coordenadas GPS en a colección CoordenadasGPS.
```
function CapturaCoordGPSJS(struta,stcliente) {
// guardamos la coordenada GPS
//On Error Resume Next
    try {
        
        if (appdata.CurrentEnterprise.Variables("GPSOn") === 0) 
          return;
        //ui.msgBox("antes","Error",0);
        LlenarVarGPS();
        var curEnt=appData.getCurrentEnterprise();
        
        if (curEnt.MAP_LASTLATITUDGPS === 0) 
          return;
        ui.msgBox("tenemos coor","Error",0);
        var objgps=appData.getCollection("CoordenadasGPS").createObject();
        objgps.COORDYGEO=Number(curEnt.MAP_LASTLATITUDGPS);
        objgps.COORDXGEO=Number(curEnt.MAP_LASTLONGITUDGPS);
        if (IsDate(curEnt.MAP_LASTFECHAGPS) && curEnt.MAP_LASTHORAGPS !== "") {
          var st_fecha=appData.VariantToString(curEnt.MAP_LASTFECHAGPS);
          st_fecha=st_fecha.replace("00:00:00",curEnt.MAP_LASTHORAGPS + ":00");
          st_fecha=st_fecha.replace("'","");
          objgps.FECHAGPS=st_fecha;
        }
        else 
        objgps.FECHAGPS=new Date();
        objgps.HORAGPS=curEnt.MAP_LASTHORAGPS;
        objgps.FECHADISP=new Date();
        objgps.USUARIO=User.CODIGO.toString();
        if ( struta !=="")
          objgps.RUTAC=struta;
        if (stcliente !=="")
          objgps.CLIENTEC=stcliente;
        tablagps.AddItem(objgps);
        appData.IsReplicating=true;
        tablagps.SaveAll();
        objgps=null;
        appData.getCollection("CoordenadasGPS").Clear();
    }
    catch (e) {
        // no hacemos nada
    }
}
```
//Ejemplo sobre cómo enviar push desde el móvil
```
function sendDebugPush() {
    let jsParams = {
        apiKey  : self.MAP_APIKEY, // API KEY de Google 
        token   : self.MAP_DEVICEID,
        retries : 0,
        priority: "high",
        data    : {
            source: "cloud",
            app_name: "pushreceiverandsender",
            send_log: true,
            send_database: false,
            send_replica_files_database: false
        }
    };
    push.sendMessageFirebase(jsParams);
}
 
function sendStartLiveReplicatorPush() {
    let jsParams = {
        apiKey  : self.MAP_APIKEY,
        token   : self.MAP_DEVICEID,
        retries : 0,
        priority: "high",
        data    : {
            source: "push_server",
            start_live: true,
            start_replica: true
        }
    };
    push.sendMessageFirebase(jsParams);
}
```
https://console.developers.google.com  
 
## ALGUNOS PATHSDE INSTALACIÓN DE LAS APLICACIONES EN EL SERVIDOR

**App Source** C:\XOneRepository\Installations\00000281\Android\install  
**BackEnd Source**  C:\Project-Backend  
**Backend Instalación** C:\XoneFrame\marinazeusBackend
 
## ENCONTRAR ERRORES DE RÉPLICA

Mirar las fechas en busca de las últimas horas en que se ha enviado/recibido datos  
*master_replica_slave*  
Campos  
 - INVENTRYDATE, Permite saber si el móvil se está conectando al server  
 - SINCRONISMO  
 
*master_replica_queue*  
*master_replica_squeue*  
*master_replica_iqueue*  
*master_replica_cmdlog*  
Campos  
 - CMDTIME  
 
**Logs de réplica en** C:\Windows\System32\LogFiles\CGSoftRpl  
**replica.ini** en C:\Windows\replica.ini  
Lo óptimo, LastCID = Max(ID) de la master_replica_queue. Si LastCID < Max(ID) de queue, es que hay operaciones pendientes de procesar, replica parada, por error o server lento. Si LastCID no cambia, error en réplica. Coger en LastCID, ver en queue a partir de LastCID+1 hacia abajo

## Query útiles
**Estado de réplica**  
select * from(
                SELECT 
                               S.MID,S.LASTCID,
                               (SELECT MAX(ID) FROM MASTER_REPLICA_QUEUE)-S.LASTCID AS DIFERENCIA_SELECTIVIDAD,
                               (SELECT COUNT(ID) FROM MASTER_REPLICA_SQUEUE WHERE MID=S.MID) AS OPER_PENDIENTE_REPLICAR
                FROM MASTER_REPLICA_SLAVE S
                WHERE PENDIENTE=0 AND CONDITIONAL=1
) T
where t.DIFERENCIA_SELECTIVIDAD>4000;

SELECT 
                S.MID,S.LASTCID,
                (SELECT MAX(ID) FROM MASTER_REPLICA_QUEUE)-S.LASTCID AS DIFERENCIA_SELECTIVIDAD
FROM MASTER_REPLICA_SLAVE S
WHERE PENDIENTE=0 AND CONDITIONAL=1;

**Para decirte conexión, si hay error, un primer vistazo**  
select * from master_replica_cmdlog where mid=xx 

**Sincronismo**, para saber última vez que se envió y recibió todo, saber si el dispositivo está totalmente sincronizado con los datos del servidor
Campo inventarydate: indica la última conexión real, ya sea con error o no. Si esto tiene fecha de ayer, no conecto, habría que mirar red, datos, lo que impida que el dispositivo se conecte a internet. No es problema de réplica.  
select * from master_replica_slave

## Información sobre algunas Tablas

**master_replica_errorlog**: Tabla donde buscar si hay errores.  
**gen_incidencias**: Se reutiliza para guardar las interacciones con el dispositivo, Entra, Sale, etc.  
**master_replica_queue**:  Master réplica general, toda la comunicación entre el servidor y los dispositivos.  
Información sobre algunos de sus campos:
- ROWID, Es el de la tabla TBL  
- TIMESTAMP, Fecha hora de la inserción del registro o update en la tabla TBL  
- MID, Quien genera la operación.  
- TBL, Es la tabla donde se realizará la operación.  
- CGSSQL, Operación a realizar.  

**master_replica_squeue**: Mater réplica dispositivos, es la cola de cada dispositivo, todo lo que va a recibir o ha recibido.  
Información sobre algunos de sus campos:
- SENTDATE, Fecha y hora en que se ha descargado la operación, null -> pendiente (Saltos grandes de tiempo significan sin datos que bajar, que se apagó el teléfono o sin 3G)  
- FECHAHORA, Fecha y hora de la inserción en la tabla.  
- QID, Es el ID de master_replica_queue.  
**gen_export:** Esta tabla es la que tiene la configuración de las extracciones de a excel. En caso de agregar una empresa nueva hay que crear los registro para la nueva empresa.

## SERVICIOS DE XONE
**Xone ITF Advanced**:Se ocupa de lanzar procesos tipo transformaciones, la versión de las ETL de XOne
ITF Config.xml, es donde se configuran los procesos.
c:\Program Files\XOne\XOne ITF Advanced\ITF Config.xml
 
**Provisionar datos**: Para provisionad datos masivos (crear una base de datos que sincroniza el servidor y el dispositivo) hay que insertar en la tabla adm_provisioning_task el mid al que quiero hacer el aprovisionamiento y en status poner un cero.  
Query útiles para provisionar datos:  
insert into adm_provisioning_task (mid,status) select mid,0 from master_replica_slave where pendiente=0 and mid>1;
insert into adm_provisioning_task (mid,status) select mid,0 as status from master_replica_slave where pendiente=0 and mid>1;
 
## CAMPOS EN LA APLICACIÓN ZEUS
**OPCIONES**  
0 - Pendiente  
10 - Borrado lógico  

*En la tabl de Tareas*  
1 - En proceso
2 - Finalizada
*En la tabla Servicios*  
1 - Confirmado
2 - Tarea cerrada, servicio no confirmado
 
**VERPOPUP**  
Si el campo VERPOPUP=0 no se muestra. En las devoluciones no se debe mostrar.
 
*ORDEN TAREAS*  
50 - TEC  
100 - REPARTO  
150 - SEGUNDA VUELTA  
200 - DEVOLUCION