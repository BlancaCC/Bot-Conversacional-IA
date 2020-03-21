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
