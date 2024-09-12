# Resoluciones

## Ejecirio 0

Antes de comenzar, reparar el grafo porque la propiedad Idiomas de un Pais está en formato string, pero debería estar en formato  Arreglo De Strings, ya que debe contener una colección con los idiomas que se habla oficialmente en un Pais. Para ello, ejecutar la siguiente sentencia:

```sql
MATCH (n :Pais)
SET n.Idiomas= split( replace(replace( replace(n.Idiomas, '[', ''), ']', ''), '"', '') , ',' )
```

## Ejercicio 1

Escribir una consulta para conocer los Paises donde se habla más de 2 idiomas y uno de ellos es 'Italiano'

**Solución**

```sql
MATCH (p:Pais)
WHERE size(p.Idiomas)>2 AND "Italiano" IN p.Idiomas
RETURN p, p.Idiomas
```

## Ejercicio 2

Calcular los pares de personas que residen en la misma ciudad. 



## Ejercicio 3

¿Por qué esta variante devuelve 0 matching? ¿Cuál es el error?

```sql
MATCH (p1: Persona) -[e :resideEn]-> (c :Ciudad),
      (p2 :Persona) -[ e :resideEn] -> (c :Ciudad)
RETURN  p1, p2, c
```

**Solución**

```sql
MATCH (p1: Persona) -[ :resideEn]-> (c1 :Ciudad),
      (p2 :Persona) -[ :resideEn] -> (c2 :Ciudad)
WHERE c1=c2
RETURN  p1, p2, c1
```

## Ejercicio 9

Proponer una consulta Cypher para calcular la longitud promedio de las “llamadas” emitidas entre cada par de teléfonos que se comunicaron en el tiempo. En la respuesta deberá aparecer:  Nro de teléfono del emisor,  Nro de teléfono del receptor  y el promedio de dichas duración.   Ordenar el resultado por  ambos números de teléfono. 


**Solución**

```sql
MATCH (ne:Tel) - [l:llamada]-> (nr:Tel)
RETURN ne.Nro as emisor, nr.Nro as receptor, avg(l.Duracion)
```

## Ejercicio 10

Parecido al anterior, pero sumarizar la duración de la llamada en sí, sin importar quien la inició. 
Es decir, en el resultado anterior estos 2 teléfonos se llaman entre sí:

**Solución**

Sin el **->**

```sql
MATCH (ne:Tel) - [l:llamada]- (nr:Tel)
WHERE ne.Nro < nr.Nro
RETURN ne.Nro as emisor, nr.Nro as receptor, avg(l.Duracion)
ORDER BY avg(l.Duracion)
```


## Ejercicio 11

Ahora se quiere hacer la agregación anterior (la que no diferencia quién inicia la llamada), pero haciendo rollUp a Persona. Es decir, si tenemos 

**Solución**

```sql
MATCH (pe:Persona)-[]-(ne:Tel) - [l:llamada]- (nr:Tel)-[]-(pr:Persona)
WHERE pe.ID < pr.ID
RETURN pe.Nombre as emisor, pe.Nombre as receptor, avg(l.Duracion)
ORDER BY emisor, receptor
```


## Ejercicio 12

Técnicamente hablando un teléfono no puede llamar al mismo. Sin embargo, sí es posible que un teléfono envíe un sms al mismo teléfono.

Proponer una consulta que muestre si hay alguna Persona que se envió mensajes de texto a sí misma .

Con el grafo deberíamos obtener a Juana.

**Solución**

```sql
MATCH (pe:Persona)-[]-(ne:Tel) - [s:sms]- (nr:Tel)-[]-(pe:Persona)
//Puede ir o no el WHERE
//WHERE pe.ID=pe.ID
RETURN distinct pe.Nombre as emisor, pe.Nombre as receptor
ORDER BY emisor, receptor
```



## Ejercicio 17

Proponer una consulta que muestre cada Ciudad (todas sus props) y un boolean que indique si está al este del Meridiano de Greenwich y otra que indique si está al Oeste del mismo. Obviamente esas columnas son excluyentes.

**Solución**

```sql
MATCH (c:Ciudad)
RETURN c, c.lon > 0 as este, c.lon < 0 as oeste
```


## Ejercicio 18

Proponer una consulta para calcular cuántas ciudades están al Este del Meridiano de Greenwich y cuántas al oeste. Con los datos del ejemplo, deberían obtener:

CantEste		CantOeste
7				15

**Solución**

```sql
MATCH (c:Ciudad)
RETURN c, c.lon > 0 as este, c.lon < 0 as oeste
```


## Ejercicio 19

Se quiere calcular la distancia entre Personas que se comunicaron por llamada, segun el lugar que dijeron que residían.

Para preguntar por distancia se genera un punto a partir de lon /lan y se pide distancia en KM

**Solución**

```sql
MATcH   (p1: Persona)-[r1 :resideEn]->(c1 :Ciudad),
        (p2: Persona)-[r2 :resideEn]->(c2 :Ciudad)
WHERE    p1.ID < p2.ID 
        AND exists( (p1)-[:registra]->(:Tel)-[:llamada]-(:Tel)-[:registra]-(p2) )
RETURN  p1, p2, point.distance(
            point({longitude: c1.lon, latitude: c1.lat}),  
            point({longitude: c2.lon, latitude: c2.lat})) / 1000 as km
```

