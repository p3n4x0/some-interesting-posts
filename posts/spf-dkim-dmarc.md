![](/assets/images/ingenieria_social/correo_logo.PNG)

El SPF (Sender Policy Framework) valida los servidores autorizados para enviar correos desde un dominio. DKIM (DomainKeys Identified Mail) firma digitalmente correos para garantizar autenticidad e integridad. DMARC (Domain-based Message Authentication, Reporting, and Conformance) combina SPF y DKIM, reduciendo el riesgo de phishing y spoofing. Estas medidas son esenciales en ciberseguridad para proteger la marca y prevenir ataques de correo malicioso.

# SPF (Sender Policy Framework)
Permite al propietario de un dominio especificar qué servidores están autorizados a enviar correos electrónicos con su dominio en el “Mail From: email address”. Ofrece la posibilidad a los receptores de consultar los DNS para obtener el listado de autorizados para un dominio en concreto. Si el mensaje de correo procede de un servidor autorizado, el receptor puede asumir la autenticidad del mensaje.

## Respuestas
Las respuestas que se pueden dar a una consulta del SPF son las siguientes:

| Respuesta    | Descripción |
|--------------|--------------|
| ***None:*** | No se encontró ningún registro SPF para el dominio. |
| ***Neutral:*** | El propietario del dominio declaró en el registro SPF que no desea afirmar que la dirección IP está autorizada a enviar desde el dominio.  |
| ***Pass:*** | La dirección IP está autorizada para enviar desde el dominio.  |
| ***Fail:*** |  La dirección IP no está autorizada para enviar desde el dominio. El registro SPF no contiene el servidor de envío ni la dirección IP utilizada para enviar correos electrónicos al proveedor del buzón.  |
| ***Softfail:*** | La dirección IP puede o no estar autorizada para enviar desde el dominio. |
|  ***Temperror:*** | A la hora de configurar este protocolo en el registro TXT, hay que tener en cuenta los siguientes valores: Ocurrió un error temporal durante el proceso de verificación SPF. Este resultado se debe, a menudo, a cuestiones técnicas que tuvieron lugar durante el proceso de verificación. Los temperror no significan necesariamente que el registro SPF no sea válido.  |
| ***Permerror:*** | El registro SPF publicado no pudo ser verificado por el proveedor del buzón |

## Valores
A la hora de configurar este protocolo en el registro TXT, hay que tener en cuenta los siguientes valores:


| Valor   | Descripción                                    |
|---------|-----------------------------------------------|
| v       | Versión de SPF utilizada.                     |
| a       | Declara la IP a la cual resuelve un dominio (normalmente se deja en blanco para asignar la IP del dominio).|
| ptr     | Se asegura de que la IP está bien configurada |
| mx      | Declara la IP a la que resuelve el registro MX (normalmente se deja en blanco para asignar la del dominio).|
| include | Incluye el registro SPF de un dominio en otro. |
| ip4     | Declara una IP específica.                     |
| ip6     | Declara una IPv6 en vez de una IPv4.           |
| all     | Es el que indica qué hacer con un correo que llega desde una IP que no ha sido indicada en el registro.|
|         | ***+ (pass):*** si no se pone ningún valor, este es el que se utiliza por defecto. Indica que las direcciones IP declaradas están autorizadas para enviar correos desde el dominio.| 
|         | ***- (fail):*** indica que las direcciones IP declaradas no están autorizadas para enviar correos desde el dominio y que los correos deben rechazarse.| 
|         | ***~ (softfail):*** indica que las direcciones IP declaradas no están autorizadas para enviar correos desde el dominio, pero que puede que lo estén más adelante, por lo que los correos deben aceptarse como sospechosos. |
|         | ***? (neutral):*** se recomienda aceptar los correos, ya que no se tiene ninguna información acerca de las IP declaradas.|

#### Ejemplo:
```
deloitte.es. 478 IN TXT "v=spf1 ip4:185.90.28.0/24 ip4:62.14.236.154 ip4:170.194.0.0/16 ip4:68.232.128.0/19 ip4:155.56.221.13 ip4:155.56.221.14 -all
```

Este registro indica que, además de la dirección IP a la que resuelve el dominio “Deloitte.es”, los rangos de direcciones IP declarados están aceptados para el envío de correos, pero cualquier otro correo que utilice el nombre “Deloitte.es” y no salga de ninguna de las direcciones declaradas debe ser rechazado. La comprobación del registro TXT de un dominio es pública, y es tan fácil como lanzar este comando, entre otras muchas opciones:

```bash
dig deloitte.es TXT
```

# DKIM (Domainskeys Identified Mail)
Mediante el uso de la criptografía, permite asociar un nombre de dominio con un mensaje de correo electrónico. Los remitentes insertan una firma digital criptográfica en los mensajes de correo para que los receptores puedan verificarla mediante la consulta de la clave pública en los DNS. Si la verificación es exitosa, DKIM proporciona un identificador de nivel de dominio fiable que sobrevive al reenvío (a diferencia de SPF).


## Respuestas
Las respuestas que se pueden dar a una consulta del DKIM son las siguientes:

| Respuesta    | Descripción |
|--------------|--------------|
| ***None:*** | El mensaje no estaba bien firmado. |
| ***Neutral:*** | El mensaje estaba firmado, pero la firma o firmas contenían errores de sintaxis o no podían procesarse de otro modo.  |
| ***Pass:*** | El mensaje fue firmado y la firma o firmas fueron aceptadas y pasaron las pruebas de verificación. |
| ***Fail:*** |  El mensaje estaba firmado y la firma o firmas fueron aceptadas, pero no superaron la prueba de verificación.  |
| ***Policy:*** | El mensaje estaba firmado, pero la firma o firmas no fueron aceptadas. |
|  ***Temperror:*** | El mensaje no se pudo verificar debido a algún error que probablemente sea de naturaleza transitoria, como la incapacidad temporal de recuperar una clave pública. Un intento posterior puede producir un resultado final. |
| ***Permerror:*** | El mensaje no se pudo verificar debido a algún error que es irrecuperable, como la ausencia de un campo de encabezado obligatorio. Es poco probable que un intento posterior produzca un resultado final. |

# DMARC (Domain-based Message Authentication, Reporting & Conformance)
Este estándar trabaja en conjunción con SPF y DKIM, permite al propietario de un dominio obtener visibilidad y control sobre el correo electrónico enviado en su nombre y especifica qué métodos de autenticación están en uso y cómo los receptores del correo deben manejarlo si dichos métodos de autenticación fallan.

En la siguiente imagen se muestra un esquema resumido del funcionamiento de DMARC con el fin de facilitar su comprensión.

![](assets/images/ingenieria_social/dmarc.PNG)


Como beneficio adicional, los ISP (internet service provider) envían un reporte de los correos electrónicos que no superan la validación de las cuentas configuradas con el objetivo de que se pueda identificar si se trata de casos de suplantación, de phishing o, simplemente, de una mala configuración que permite estudiar la volumetría frente a los logs del MTA (mail transfer agent).

De cara a la implementación de DMARC, es muy importante tener bien configurados y bajo total control los dominios que se utilizan para el envío de correos electrónicos, ya que, si se implementa DMARC, pero no SPF y DKIM, se corre el riesgo de bloquear los propios correos electrónicos.

Los reportes de DMARC pueden llegar en dos formatos: RUA (reporting URI of aggregate reports) o RUF (reporting URI of forensics reports).

## RUA

Como se ha indicado previamente, mediante la utilización de criptografía, permite asociar un nombre de dominio con un mensaje de correo. Los remitentes insertan una firma digital criptográfica en los mensajes de correo para que los receptores puedan verificarla mediante la consulta de la clave pública en los DNS. Si la verificación es exitosa, DKIM proporciona un identificador de nivel de dominio fiable que sobrevive al reenvío (a diferencia de SPF).

De esta manera, se pueden obtener estadísticas y la volumetría de la cantidad de correos que se envían utilizando el dominio indicado. Además, es posible indicar cuáles de ellos superan las validaciones SPF y DKIM.

De estos datos se puede extraer información tan valiosa como a qué ISP se envían más correos, desde qué direcciones IP, desde qué país, la estadística de validación…
## RUF

Este estándar trabaja en conjunción con SPF y DKIM y permite al propietario de un dominio obtener visibilidad y control sobre el correo electrónico enviado en su nombre. Especifica qué métodos de autenticación están en uso y cómo los receptores del correo deben manejarlo si dichos métodos de autenticación fallan.

Estos reportes son los más completos, ya que son una copia del propio correo electrónico. No obstante, son muy pocos los ISP que envían esta información, probablemente por la actual ley de protección de datos.
## Valores

| Valor    | Descripción |
|----------|--------------|
| ***v*** | Versión del protocolo. |
| ***p*** | Política del dominio. ***None:*** no realiza ninguna acción específica en los correos. ***Quarantine:*** deja en cuarentena los correos en los que las verificaciones fallen. ***Reject:*** rechaza los correos en los que las verificaciones fallen. |
| ***pct*** | Porcentaje de correos sujetos a filtrado. |
| ***fo*** | Indica qué tipo de mensajes son los deseados.| 
|          | ***0:***  genera un report de fallo si SPF y DKIM son fail.|
|          | ***1:*** genera un report de fallo si SPF o DKIM son fail.|
|          | ***d:*** genera un report de fallo si DKIM es fail.|
|          | ***s:*** genera un report de fallo si SPF es fail. |
| ***rua*** | Direcciones a las que se deben enviar los informes RUA. |
| ***ruf*** | Direcciones a las que se deben enviar los informes RUF. |
| ***aspf*** | Modo de alineación para SPF. |
| ***adkim*** | Modo de alineación para DKIM. |
| ***rf*** | Formato de los mensajes de fallo. |
| ***ri*** | Tiempo (en segundos) hasta que se envíe el report al remitente. |ç

A la hora de comprobar si un dominio dispone de una configuración de DMARC, basta con consultar su registro TXT:
```bash
dig _dmarc.deloitte.es TXT
```


