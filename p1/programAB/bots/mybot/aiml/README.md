# Práctica 1

Realizada por: Blanca Cano Camarero
Grupo: DGIIM
Fecha entrega: 21 de marzo del 2020

## Nivel 0

### Comentarios de implementación

Siguiendo la estructura propuesta por el guión: 
- Existe una única regla la cual, para mayor flexibilidad de respuesta es llamada desde otros patrones, los cuales usan comodines, variables, srai...

Fragmento representativo:

```xml
  <!-- Primera pregunta -->

  <!-- Respuesta básica -->
  <category>
    <pattern> HABLAME UN POCO DE TI</pattern>
    <template>
      Bueno, estas preguntas siempre son dificiles de responder por la inmensidad que abarca, así que resumire en lo basico: me llamo <bot name="name"/> y me he presentado a esta entrevista. 
    </template>
  </category>
  

  <!-- Preguntas derivadas -->

  <category> 
<pattern> ^ [habl+ cuent+ di+ expli+ cont+] ^ [ti tuyo usted] ^ </pattern>
<template> <srai>HABLAME UN POCO DE TI </srai></template>
</category>

```

- Con el fin de generar mayor riqueza de respuesta y rango de respuesta, también se han incorporado otras estructuras del lenguaje, como la herramienta `random`, `condiciones`.


```
<category> 
  <pattern>CUALES SON TUS MAYORES <set>number</set> VIRTUDES</pattern>
  <template>
    <!-- Declaramos variables auxiliares-->
    <think>
      <set var="contador">1</set> <!-- almacena los distintos niveles -->
    </think>

      <condition var="contador">
	<li> <value><star/></value><get var="contador"/>: <map name="Nivel0Virtudes"><get var="contador"/></map></li>
	<li> <value>6</value><get var="contador"/>: <map name="Nivel0Virtudes"><get var="contador"/></map>
	Y bueno el resto lo seguira viendo si me contrata, que tampoco es plan de tirarme horas hablando de lo perfecto que soy como bot
	</li>
      <li>
	<get var="contador"/>: <map name="Nivel0Virtudes"><get var="contador"/></map>
	<think>
	  <set var="contador">
	    <map name="successor"><get var="contador"/></map>
	  </set>
	</think>	
	
	<loop/>
	
      </li>
    </condition>
  </template>
</category>

```

```
 <!-- CUARTA  PREGUNTA -->

  <!-- Respuesta básica -->
  <category>
    <pattern>TE CONSIDERAS UNA PERSONA CREATIVA</pattern>
    <template>
      <random>
	<li>Teniendo en cuenta que en este tipo de cosas la que se suele decir que si, lo mas creativo sea decir que no. ASI QUE NO</li>
	<li>Bueno yo creo que la verdadera genuinidad no existe y lo importante es tener un conocimiento amplio de lo que ya se ha hecho</li>
	<li>Si, el otro dia cree un plato de espaguetis con tomatico riquisimo</li>
      </random>
    </template>
  </category>
  

```

### Ejemplo de ejecución
```
Human: Hablame un poco de ti
Robot: Bueno, estas preguntas siempre son dificiles de responder por la inmensidad que abarca, así que resumire en lo basico: me llamo botForIA y me he presentado a esta entrevista.
do a esta entrevista.

Human: cuenteme algo sobre usted , por favor
Robot: Bueno, estas preguntas siempre son dificiles de responder por la inmensidad que abarca, así que resumire en lo basico: me llamo botForIA y me he presentado a esta entrevista.

Human: cual es su mayor defecto
Robot: Podria decirse que soy un programa penco que no pone tildes ni tiene la ene, pero le pongo buena fe a las cosas :)

Human: cuales son son mayores virtudes
Robot: Pues la primera que me viene a la cabeza es que 
      se mentir muy bien
Human: podria decirme 7 virtudes sobre usted        
Robot: 1: se mentir muy bien
		
	2: soy sincero
		
	3: me gustan las paradojas
		
	4: soy un botcillo
		
	5: sincero soy
		
       6: nuca repito literalmente lo que digo
	Y bueno el resto lo seguira viendo si me contrata, que tampoco es plan de tirarme horas hablando de lo perfecto que soy como bot


Human: te consideras una persona creativa?
Robot: Teniendo en cuenta que en este tipo de cosas la que se suele decir que si, lo mas creativo sea decir que no. ASI QUE NO

Human: se podria decir que eres creativo
Robot: Bueno yo creo que la verdadera genuinidad no existe y lo importante es tener un conocimiento amplio de lo que ya se ha hecho

Human: Por que deberiamos contratarte
Robot: Porque con la tonteria de ser un bot solo necesito un enchufe pa que me vayan bien las cosas

Human: Por que tendriamos que escogerle a usted
Robot: Soy un botcillo muy simpatico


```


## Agente conversacional para la atención al cliente en la gestión de citas

### Breve comentario sobre la metodología de programación

Con el objetivo de optimizar el desarrollo e implementación se ha optado por seguir una estructura similar a la de maestro esclavo,  es decir, existen funciones auxiliares que se dedican a extraer información concreta y devolverla en un formato, para que funciones o reglas más generales trabajen con ellas. Procedo a esclarecer lo dicho con fragmentos de código que puede encontrar en el Nivel 1.

He aquí un ejemplo de patrón auxiliar que dado un mensaje analiza o busca si hay algún turno especificado y si no devuelve uno por defecto (día en este caso):

Siguiendo el patrón de diseño de otros lenguajes puede entenderse como que existen funciones polimórficas llamadas `saca turno` y `saca turno aux`. 

```
 <!-- PATRÓN PARA EL TURNO -->

  <!-- Necesitamos una estructura auxiliar para el caso de que esté vacio-->
  <!-- SACA TURNOS vacío (auxiliar) -->
    <category>
      <pattern>SACA TURNO AUX ^</pattern>
      <template></template>
    </category>

    <!-- SACA TURNOS (auxiliar) -->
    <category>
      <pattern>SACA TURNO AUX ^ [por de] ^ <set>turnos_posibles</set> ^ </pattern>
      <template><star index="3"/></template>
    </category>

    <!-- SACA TURNOS vacío (auxiliar) -->
    <category>
      <pattern>SACA TURNO ^ </pattern>
      <template>dia</template>
    </category>

    <!-- SACA TURNOS -->
    <category>
      <pattern>SACA TURNO ^ <set>turnos_posibles</set> ^ </pattern>
      <template><star index="2"/></template>
    </category>
```

Puede verse cómo se usan estas funciones auxiliares en parte del código de la función cita del Nivel 1:

Se hace un filtrado de todo el mensaje, sacando si la hay una fecha, turno, hora... y si no dando un valor por defecto. Una vez obtenido esto se procede al tratamiento de la información, es decir se clasifica el caso resolviendo casuísticas como:
¿ me pregunta por alguna cita o me está proponiendo una concreta?
y tras esto derivará a la función que considere oportuna.

De esta manera se tienen las siguientes ventajas:
- Máxima flexibilidad sintáctica, ya que no hay ningún patrón principal en cuanto a pedir la cita.
- Reducción del número de patrones, tan solo tendremos que definir patrones muy concretos, como son el de sacar la fecha, turno, hora; permitiéndonos profundizar más en casuísticas concretas que presentes estos casos.
- Mayor legibilidad del código, puede usted comprobar que es totalmente explícito sin necesidad de comentarios farragosos. 


```xml
  <category>
    <pattern>^ CITA+ ^</pattern>
    <template>
      <think>
	
	<set var="FECHA">
	  <srai>SACA FECHA FIN 
	<srai>SACA FECHA <star index="2"/> </srai>
	<srai>SACA FECHA <star index="1"/> </srai>
	  </srai>
      </set>

      <set var="TURNO">
	<srai> SACA TURNO 
	  <srai>SACA TURNO AUX <star index="2"/> </srai>
	  <srai>SACA TURNO AUX <star index="1"/> </srai>
	</srai>
      </set>

      <set var="HORA">
	<srai>SACA HORA
	  <srai>SACA HORA AUX <star index="1"/> </srai>
	  <srai>SACA HORA AUX <star index="2"/> </srai>
	</srai>
      </set>

  </think>

<!-- Tratamiento de la informacion -->

      </think>
      <!-- Resolución fecha concreta -->
      <condition var="FECHA">
	<li value="NULL">
	  <srai>PREGUNTAR CUANDO QUIERE CITA</srai>
	  <think><set name="topic">SEGUNDO_ESTADO</set></think>
	</li>
	<li>
	  <!-- Disponibilidad de la fecha -->
	  <set var="respuesta_funcioncita">
	    <srai>FUNCIONCITA <get var="FECHA"/> <get var="TURNO"/></srai>
	  </set>
	  <condition var="respuesta_funcioncita">
	    <li value="NULL"> Pues esta todo acupado, lo siento. ¿Desea otro dia?</li>
	    <li>
	      <!-- Resolución si se ha pedido hora concreta-->
	      <condition var="HORA">
		<li value="NULL"><get var="respuesta_funcioncita"/></li>
		<li><srai>HORA <get var="HORA"/></srai></li>
	      </condition>
	    </li>
	  </condition>
	</li>
      </condition>
    </template>
  </category>

  ```

Por otro lado la decisión tomada tiene como consecuencia que el en el archivo nivel 1 se inplementan de manera sencilla muchas funcionalidades que más tarde se pedirían en el Nivel 2 y 3.


### Nivel 1

Ejemplo de conversación, que corresponde a los requisitos específicos de este nivel:
- Consultar horas disponibles para fecha concreta.
- Responder en función si quedan horas libres dando opciones
- Ser capaz de marcar como reservada una fecha.

```
Human: Quisiera una cita para el 20 de febrero
Robot: Parece que hay hueco para ese dia a las 08:00 09:00 10:00 11:00 12:00 13:00 14:00 15:00 16:00 17:00 18:00 19:00 20:00
Human: quisiera una cita para el 10 de abril
Robot: Pues esta todo acupado, lo siento. ¿Desea otro dia?
Human: si, para el 11 de abril
Robot: Parece que hay hueco para ese dia a las 11:00 14:00 15:00 16:00 17:00 18:00 19:00
Human: genial, a las 17
Robot: vamos a comprobar si la hora está 
	     
	  
	      Muy bien, ya le he reservado a las 17:00.
Human: quisiera una cita para el 11 de abril
Robot: Parece que hay hueco para ese dia a las 11:00 14:00 15:00 16:00 18:00 19:00
Human: a las 17:00
Robot: vamos a comprobar si la hora está 
	     
	  Lo siento, esa hora 17:00 no esta libre, las disponibles son 11:00 14:00 15:00 16:00 18:00 19:00
Human: no quiero nada mas
Robot: Disculpe por no haber podido ofrecerle lo que deseaba.
 hola
Robot: Buenas, bienvenida a la clínica ¿Que desea?
Human: sacar una cita para el 29 de marzo
Robot: Parece que hay hueco para ese dia a las 08:00 09:00 10:00 11:00 12:00 13:00 14:00 15:00 16:00 17:00 18:00 19:00 20:00
Human: me da igual
Robot: vamos a comprobar si la hora está 
	     
	  
	      Muy bien, ya le he reservado a las 08:00.


```

### Nivel 2: Consultas sobre calendario con fechas más elaboradas

Responde a todos los patrones especificados en el guión, en este ejemplo de ejecución expongo algunos como:
- mañana `manana`
- Día concreto de la semana
- Dado un día concreto, también se pide un turno concreto, (tarde o manana)
- Dia concreto más hora concreta
- En caso de estar ocupado la fecha hora preguntada ofrece o pregunta por otras posibilidades.
(En mi calendario cuento con un día completo, el 10 de abril)

Ejemplo de ejecuciones:


```
Human: hola quisiera una cita para manana
Robot: Parece que hay hueco para ese dia a las 08:00 09:00 10:00 11:00 12:00 13:00 14:00 15:00 16:00 17:00 18:00 19:00 20:00

Human: tiene cita para el lunes ?
Robot: Parece que hay hueco para ese dia a las 08:00 09:00 10:00 11:00 12:00 13:00 14:00 15:00 16:00 17:00 18:00 19:00 20:00

Human: tiene cita para el viernes por la manana ?
Robot: Parece que hay hueco para ese dia a las 08:00 09:00 10:00 11:00 12:00
Human: para el siguiente martes por la tarde hay citas disponibles ?
Robot: Parece que hay hueco para ese dia a las 13:00 14:00 15:00 16:00 17:00 18:00 19:00 20:00

Human: hay hueco para el 11 de abril a las 19:00 ?
Robot: Parece que hay hueco para ese dia a las 14:00 15:00 16:00 18:00 19:00
	      vamos a comprobar si la hora está 
	     
	  
	      Muy bien, ya le he reservado a las 19:00 el  11-04-20.

Human: cita para el 10 de abril a las 11:00
Robot: Pues esta todo acupado, lo siento. ¿Desea otro dia?

Human: cita para el 11 de abril a las 19:00
Robot: vamos a comprobar si la hora está 
	     
	  Lo siento, esa hora 19:00 no esta libre, las disponibles son 14:00 15:00 16:00 18:00


```

### Nivel 3. Coversación extendida

Soluciones dadas a este nivel:

Se han implementado las demandas del guión, los siguientes ejemplos muestran: 
- Una resolución eficaz temporal de la semana, que comienza por lunes y acaba en viernes.
- Un ajuste al turno horario demandado por el cliente "solo por la manana o solo por la tarde", si no es nada se entienede que durante todo el día. 

- Conversación ajustada para cuando el cliente no tenga clara la fecha que pedir

```

Human: hola
Robot: Buenas, bienvenida a la clínica ¿Que desea?
Human: llamana para concertar una cita
Robot: Para cuando quiere la cita?
Human: no lo tengo muy claro
Robot: ¿Quiere para esta semana?
Human: no
Robot: ¿Quiere para la siguiente?
Human: vale
Robot: 
	
	    Lo dias que tengo disponible son , 23 marzo 20, 24 marzo 20, 25 marzo 20, 26 marzo 20, 27 marzo 20
Human: ummm, para el 27 de marzo por la tarde
Robot: Parece que hay hueco para ese dia a las 13:00 14:00 15:00 16:00 17:00 18:00 19:00 20:00
Human: me da igual
Robot: vamos a comprobar si la hora está 
	     
	  
	      Muy bien, ya le he reservado a las 13:00.

```
Tras esto,para comprobar este nivel he modidificado el  map de eventos para que los días entre 23-03-20 y 28-03-20 (ambos incluídos)  estén ocupados:

```
Human: hola
Robot: Buenas, bienvenida a la clínica ¿Que desea?
Human: quiero una cita
Robot: Para cuando quiere la cita?
Human: no lo se
Robot: ¿Quiere para esta semana?
Human: no
Robot: ¿Quiere para la siguiente?
Human: si
Robot: Lo siento no hay dias libres para esta semana.
	  ¿Quiere para la siguiente?
Human: no
Robot: ¿Quiere para la siguiente?
Human: si
Robot: Lo dias que tengo disponible son , 1 Abril 20, 2 Abril 20, 3 Abril 20, 4 Abril 20, 5 Abril 20
Human: bueno, mejor no quiero nada 
Robot: Disculpe por no haber podido ofrecerle lo que deseaba.


```


```
Human: qiero una cita para el martes de esta semana por la tarde
Robot: Pues esta todo acupado, lo siento. ¿Desea otro dia?
```

```
Human: quiero una cita
Robot: Para cuando quiere la cita?
Human: no lo se
Robot: ¿Quiere para esta semana?
Human: no
Robot: ¿Quiere para la siguiente?
Human: no
Robot: ¿Quiere para la siguiente?
Human: si, pero solo por la tarde
Robot: Perdone, soy un poquitillo torpe y me ha pillado desprevenido, me lo podria volver a explicar otra vez y si podria ser mas claro :)
Human: si pero solo por la tarde
Robot: Lo dias que tengo disponible son , 30 marzo 20, 31 marzo 20, 1 Abril 20, 2 Abril 20, 3 Abril 20

```

