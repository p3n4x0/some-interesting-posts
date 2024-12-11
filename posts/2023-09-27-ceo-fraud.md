![](/assets/images/ingenieria_social/Ceo_logo.png)
# Introducción
Aunque la ingeniería social es utilizada en muchas tipologías distintas de fraude como vector de entrada para tratar de aumentar el éxito en las campañas, hay ciertos tipos de fraude que se basan exclusivamente en el mal uso de esta técnica.

Entre estos fraudes, el más popular actualmente es el CEO fraud, *que se detallará a continuación*, aunque también hay muchos otros casos en los que, simplemente, se realiza un estudio digital de un objetivo (ya sea un particular o una empresa) y se estructura el ataque en función del vector de entrada más débil que se haya encontrado.

El CEO fraud es un tipo de fraude en el que se suplanta al director ejecutivo de una empresa para instar - mediante la presión que un alto cargo puede ejercer- a un empleado que disponga de acceso a la contabilidad o a las transferencias de la compañía a realizar pagos de manera urgente y confidencial.

# Motivación
### ¿Por qué se utiliza este tipo de fraude? 
La respuesta es simple: se puede llevar a cabo sin apenas conocimiento técnico, de manera rápida, frente a cualquier compañía y sin dedicarle prácticamente recursos.
### ¿Cuáles son los motivos del éxito de este tipo de fraude? 
Estos cuatro términos lo definen perfectamente: miedo, amenaza, expectativa y curiosidad. Aunque todos piensan que en ningún caso podrían ser víctimas de un fraude de este tipo, es uno de los que mayor éxito tienen.

# Caso real
Para comprender mejor en qué se basa el fraude del CEO y sus características se va a presentar una investigación contra una compañía y su director ejecutivo; en este caso, contra **Tom Kemp, CEO de Centrify**, que fue víctima de un fraude de este tipo, como él mismo ha mencionado en infinidad de ocasiones, llegando incluso a plasmarlo en un blog. 1 En él pueden verse exactamente los correos del fraude real y anima a poner su caso como ejemplo para luchar contra esta estafa.

## 1.Busqueda del director ejecutivo
En primer lugar, basta con hacer una simple búsqueda en Google para conocer quién es el director ejecutivo de la compañía Centrify. 

![](/assets/images/ingenieria_social/Ceo1.PNG)

Tenemos el primer dato:
- Nombre del director ejecutivo: ***Tom Kemp.***
- LinkedIn del director ejecutivo: [https://www.linkedin.com/in/tomkemp](https://www.linkedin.com/in/tomkemp)

## 2.Busqueda del director financiero
Para dar más credibilidad a la situación, *dado que puede ser algo inusual que un director ejecutivo contacte directamente con el Departamento de Finanzas*, se procede a identificar al director financiero (CFO) de la empresa mediante otra búsqueda simple.

![](/assets/images/ingenieria_social/Ceo2.PNG)

Datos obtenidos hasta ahora:
- Nombre del director ejecutivo: ***Tom Kemp.***
- LinkedIn del director ejecutivo: [https://www.linkedin.com/in/tomkemp](https://www.linkedin.com/in/tomkemp)

---

- Nombre del director financiero: ***Tim Steinkopf.***
- LinkedIn del director financiero: [https://www.linkedin.com/in/tim-steinkopf-00899b5](https://www.linkedin.com/in/tim-steinkopf-00899b5)


## 3.Estructura de correo
El siguiente paso en la investigación es identificar la estructura de los correos electrónicos que utilizan en Centrify. Para ello, es posible utilizar miles de fórmulas, aunque con hacer una búsqueda rápida que permita enlazar a Tom Kemp con una dirección de correo de Centrify se muestra su cuenta

![](/assets/images/ingenieria_social/Ceo3.png)

Este hallazgo permite identificar, además de la cuenta de correo del director ejecutivo, la **estructura** que utiliza dicha compañía **para la creación de correos electrónicos (“.@centrify.com”)**, lo que facilita obtener la cuenta de casi cualquier empleado.

Datos obtenidos hasta ahora:
- Nombre del director ejecutivo: ***Tom Kemp.***
- LinkedIn del director ejecutivo: [https://www.linkedin.com/in/tomkemp](https://www.linkedin.com/in/tomkemp)
- Correo del director ejecutivo: tom.kemp@centrify.com

---

- Nombre del director financiero: ***Tim Steinkopf.***
- LinkedIn del director financiero: [https://www.linkedin.com/in/tim-steinkopf-00899b5](https://www.linkedin.com/in/tim-steinkopf-00899b5)
- Correo del director financiero: tim.steinkopf@centrify.com.

## 4.Investigacion de la organización
Una vez obtenida esta información, es necesario conocer el nombre y la dirección de correo electrónico de algún empleado que trabaje en el Departamento de Finanzas y que, por tanto, disponga de acceso a la realización de transferencias.

Para ello pueden utilizarse distintas técnicas de búsqueda, como navegar por la propia red de LinkedIn o volver a realizar una búsqueda a través de Google. En este caso, se recomienda la búsqueda mediante [Google Dorks](GoogleDorks):

`site:linkedin.com centrify`

Datos obtenidos hasta ahora:
- Nombre del director ejecutivo: ***Tom Kemp.***
- LinkedIn del director ejecutivo: [https://www.linkedin.com/in/tomkemp](https://www.linkedin.com/in/tomkemp)
- Correo del director ejecutivo: tom.kemp@centrify.com

---

- Nombre del director financiero: ***Tim Steinkopf.***
- LinkedIn del director financiero: [https://www.linkedin.com/in/tim-steinkopf-00899b5](https://www.linkedin.com/in/tim-steinkopf-00899b5)
- Correo del director financiero: tim.steinkopf@centrify.com.

---

- Correo del empleado de Finanzas: d****.r********@centrify.com.

## 5.Elaboracion correo
En este punto, hay que generar un correo que sea un reenvío de otro recibido anteriormente por el director ejecutivo donde se solicite de manera urgente que se realice una transferencia por un motivo crítico y confidencial.

```
D****, 
Please make urgent the following payment. 
I have limited access to this email, contact me through my personal account
"t.speinkopf.5657@mail.com". 
I trust your discretion, it's confidential. 
From: Kemp, Tom. 
Sent: Monday, April 2, 2018 02:17 PM. 
To: Steinkopf, Tim . 
Subject: Urgent and confidential payment. 

Tim, 

We are in a critical negotiation and have to make a payment of $55496 to the following account with the concept: payment centrify - confidential. 

CC: 1234 12 1234 5678 9123 4567 

It's strictly confidential. 
It's urgent, please, it must be done before 3:00 p. m.
```

De este modo, se da a la víctima un periodo de tiempo muy pequeño para reaccionar (alrededor de 30 minutos) y, mediante la urgencia y las expectativas tras recibir un correo directo de un alto cargo en el que se solicita discreción y buen hacer para que una supuesta negociación llegue a buen puerto, se fuerza a que el pago ilegítimo se realice.

El último punto de este fraude es enviar el mensaje mediante la suplantación del correo electrónico del director financiero, para lo que se empleará la técnica de email spoofing. El término spoofing se refiere a las técnicas utilizadas para la suplantación de identidad (DNS spoofing, IP spoofing, ARP spoofing, email spoofing…). En este caso, se utiliza la de ***email spoofing,*** que es la suplantación de una cuenta de correo electrónico para el envío de mensajes maliciosos.

Para llevar a cabo esta técnica existen muchas posibilidades, aunque, al fin y al cabo, basta con disponer de un host en el que se habilite SMTP y añadir un simple código PHP en cuyas cabeceras se indique la cuenta que se pretende suplantar.

Explicacion [***Email Spoofing***](https://p3n4x0.github.io/email-spoofing/)

De esta forma, se envía un correo electrónico en el que se suplanta, a simple vista, la cuenta del director financiero. No obstante, en el caso de que la víctima acceda al código de las cabeceras, sería capaz de detectar la suplantación.




