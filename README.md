# Fútbol América vs Europa
En este mini proyecto tratamos de comprobar la declaración de Mbappe de 2022, donde indica que hay mayor nivel futbolístico en las selecciones de Europa.

## El contexto y motivación

*"Argentina y Brasil no juegan partidos de mucho nivel para llegar a la Copa del Mundo. En Sudamérica el fútbol no está tan avanzado como en Europa. Y es por eso que cuando miras las últimas Copas del Mundo siempre son los europeos los que ganan"*. Eso dijo Kylian Mbappe hace un par de años.

El Dibu le mandó a decir esto: *"Bolivia en La Paz, Ecuador con 30 grados, Colombia que no puedes ni respirar... Ellos juegan siempre en canchas perfectas, mojaditas, y no saben lo que es Sudamérica"*

Y el uruguayo Abreu se descargó con esto:
*"¿Cuántos títulos tiene Francia, y cuántas Eurocopas? Uruguay tiene dos Mundiales y quince Copas América. Le faltó Wikipedia a Mbappé. Porque ellos jueguen con europeos no significa que esos europeos son de primer nivel"*.

Puedes ver más de la polémica aquí:
https://www.emol.com/noticias/Deportes/2022/05/31/1062629/mbappe-ninguneo-clasificatorias-argumentos.html

Hay diferentes enfoques que se pueden tomar para tratar de dar respuesta a esta declaración, y creo que ninguno sería el definitvo. Pero acá haremos uno que es entretenido y bastante instructivo para soltar la mano en python.

Primero, usaremos el repositorio que tiene Statsbomb (https://statsbomb.com/es/). Podemos acceder a su data a través de github (https://github.com/statsbomb/open-data), o a través de la librería que han creado que, básicamente, lee la info que alojan en su github, con alguna limpieza previa (https://github.com/statsbomb/open-data). Por simplicidad usaremos la librería (statsbombpy).

## Cómo abordamos el problema
Statsbomb guarda mucha información de los partidos de diferentes ligas. Afortunadmante, este año (2024), por primera vez guardaron información de la Copa América (https://statsbomb.com/es/noticias/datos-gratuitos-la-copa-america-2024/). 
Principalmente usaremos los "Eventos" y los "Matches". Acpa puedes ver más detalle de la documentaciónd de cada uno:
https://github.com/statsbomb/open-data/tree/master/doc

Nuestra aproximación al problema será **comparar la calidad del juego de los equipos participantes en la Copa América 2024 vs los equipos participantes en el EuroCopa 2024**.
¿Qué define la calidad de un equipo?... es debatible y no hay un único parámetro, pero hay que tomar alguna posición, así que creamos las siguientes métricas para definir la calidad de juego de un equipo:

- **1. successful_pass_ratio**: % de los pases que llegan a destino (mientras más alto, mejor)
- **2. miscontrol_np_ratio**: % de pérdidas de balón no estando bajo presión (mientras más alto peor)
- **3. dribbles_successful_ratio**: % de dribbles que fueron completados con éxito (mientras más alto, mejor)
- **4. successful_recovery_ratio**: % de balones recuperados con éxito (mientras más alto, mejor)
- **5. successful_interception_ratio**: % de intercepciones realizadas con éxito (mientras más alto, mejor)
- **6. successful_shots_ratio**: % de los disparos al arco que efectivamente llegaron al arco, aunque haya sido palo o atrapado por el portero (mientras más alto mejor)
- **7. successful_shots_goal_ratio**:% de los disparos exitosos -o sea, de disparos que llegaron al arco (métrica 6)- que fueron gol
- **8. shots_goal_ratio**: % de disparos que fueron gol, idnependiente de que el disparo haya llegado al arco

Evaluamos cada una de estas métricas para cada partido de cada equipo. Tenemos

Equipos:
- 24 equipo en la Eurocopa
- 16 equipo en la Copa América

Partidos:
- 51 partidos en la Eurocopa
- 32 partidos en la Copa América

## Metodología y Resultados
Para cada métrica gráficamos para entender visualmente cómo se comportó cada equipo por cada partido para cada torneo. Por espacio no podré todas las gráficas acá, pero puedes revisarlas en el código, si quieres. Te dejo un par de ejemplos. En la siguiente imagen vemos cómo se distirbuyen **métricas de pase entre los equipos** de la Eurocopa vs los equipos de la Copa América. Se ve que los europeos dan más pases en total por partido, dna más pases exitosos por partido y también el % de pases exitosos por partidos es superior (de todos modos, para nuestro análisis posterior solo tomamos el ratio de pases exitosos, ya qe al ser procentaje, nos facilita la comparación independiente de la cantidad de equipos y partidos)

![image](https://github.com/user-attachments/assets/e7163d5d-d08e-4562-a1f3-a000e8050716)

Acá **el segundo ejemplo con los dribbles**. Los europoes tienen una distribución más amplia, es decir, hay equipos con mucho diribble y otros con muy poco, mientras que en América, en los equipos se ve menos amplitud en la distribución, es decir, son pocos los equipos que dribblean poco. Pero ojo, si vemos el porcentaje de dribbles exitosos, los equipos americanos parecen quedar un poco bajo los europeos (solo visualmente eh! ya vamos a entrar a picar).

![image](https://github.com/user-attachments/assets/bc45d77e-e1d9-4995-b38b-7921c33a46fc)




Así mismo generamos un tabla comparativa con las medias y las medianas de cada métrica. Esto nos permite hacer una comparativa simple entre ambos torneos, y nos deja ver luces quién es mejor en promedio (o es mejor para el 50% de los equipos superiores). No obstante, esta comparación nos puede llevar a error. Lo más correcto sería validar que esas diferencias sean estadísticamente significativas. De todos modos te dejo acá las tablas comparativas simples de las medianas y medias:

**Comparación de Medianas**

<img width="449" alt="image" src="https://github.com/user-attachments/assets/287fcb1f-a71d-46d6-a2b3-ef17ac8c9959">


**Comparación de Medias**

<img width="450" alt="image" src="https://github.com/user-attachments/assets/a94e1a7b-b934-4226-8d7b-ee85b9799a7d">


Ojo, que si nos quedaramos con esta "omparación simple", los equipos quedan empatados tanto en media como en medianas , ya que para el primer caso cada uno es mejor en 4 métricas, y en el caso de las medianas empatan en dos y son mejores cada uno en 3. Pero queremos hacer un análisis un poco más robusto que eso...

Dado lo anterior, para cada métrica aplicamos una prueba T-Student.
Consideramos nuestras dos muestras independientes. Lo que ocurre en los partidos de Eurocopa no debería afectar lo que ocurre con los partidos de Copa América.


Evaluamos normalidad de la distribución con un test de Shapiro-Wilk, y también evaluamos Heterocedatsicidad con la prueba de Levene.
Tenemos 8 métricas por 2 competiciones, por lo que tenemos 16 evaluaciones. Solo 5 pasaron el test de normalidad. Ahora, como nuestras muestras tienen más de 30 observaciones, asumiremos normalidad, de todos modos.

Evaluamos la Homocedasticidad para cada métrica con el test de Levene (8 pruebas en total, una por cada métrica). De los 8, 6 cumplen la condición. Para los dos que no, empelamos la corrección de Welch en la prueba T-Student.

Finalmente, a tener en cuenta, nuestra hipótesis nula y alternativa.


- H0: Métrica(i) Eurocopa < Métrica(i) Copa América
- H1: Métrica(i) Eurocopa > Métrica(i) Copa América

El resultado es el siguiente:

<img width="734" alt="image" src="https://github.com/user-attachments/assets/63a30172-0ecf-4c25-8b9c-358a955bcad8">




**Solo para dos métricas (successful_pass_ratio y successful_recovery_ratio) podemos rechazar la hipótesis nula, es decir, existe evidencia estadística suficiente para concluir que el % de pases exitosos y  el % de recuperaciones de balón en los equipos de la Eurocopa son, en promedio, mayores (o mejores) que en la Copa América**.
Para el resto de las métricas no existe evidencia suficiente que nos permita afirmar lo anterior, o sea, podríamos decir, simplificando el tema, que en el resto juegan similar europeos y americanos.

Esto es mala noticias para los que nos gusta más el fútbol sudaméricano (digamos "malas noticias" entre comillas), o al menos, afirma con datos la declaración de Mbappe.

##  Consideraciones adicionales y limitaciones

1. Hay que tener en cuenta que la selección de estas métricas fue discrecional. Pudiesen seleccionarse otras métricas que den mayor cuenta de la calidad de juego de un equipo.
2. Se podría argumentar también que en realidad deberia medir la calidad de juego de los equipos al jugar americanos vs europeos, y no europeos vs europeos y americanos vs americanos (aunque lo que dijo Mbappe es que llegan mejor preparados por jugar más europeos contra europeos y tener estos mayor calidad futbolística)
3. Sería interesante considerar la data consolidada de más Torneos (más Copas Américas, más Eurcocopas), lo que nos permitiría tener más muestras y hacer más robusto el análisis, pero lamentablemente, es primera vez que el proveedor de la data guarda la Copa América
4. Consideramos la data solo de dos torneos. Podrían haberse incluidos más torneos e incluso desempeños de juego en Copas de Mundo u otros.
5. Dejamos fuera una variable que podría considerarse importante como son los goles por partidos o victorias, no obstante, se quiso medir la calidad de juego independiente del resultado, que muchas veces no es justo (y...el fútbol no es justo siempre). Si incluimos ratios sobre cantidad de diparos.
6. Dejamos fuera a los arqueros y su desempeño en la considración de métricas
7. Seguramente existen otras métricas más especializadas y usadas en el fútbol hoy que por tiempo no se estudiaron e incluyeron
8. Hay muchos análisis adicionales que se pueden hacer, muchos enfoques abordar. Afortunadamente la data del fútbol es muy muy exhaustiva. Lo malo...no hay tanto tiempo disponible...
   

Al menos para esta aproximación...esta vez, ganó Francia...🇫🇷
