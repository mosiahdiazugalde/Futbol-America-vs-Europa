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
- **6. successful_shots_ratio**: % de los disparos al arco que efectivamente llegaron al arco, aunque haya sido palo o atrapado por el protero (mientras más alto mejor)
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
Para cada métrica aplicamos una prueba T-Student.
Consideramos nuestras dos muestras independientes. Lo que ocurre en los partidos de Eurocopa no debería afectar lo que ocurre con los partidos de Copa América.


Evaluamos normalidad de la distribución con un test de Shapiro-Wilk, y también evaluamos Heterocedatsicidad con la prueba de Levene.
Tenemos 8 métricas por 2 competiciones, por lo que tenemos 16 evaluaciones. Solo 5 pasaron el test de normalidad. Ahora, como nuestras muestras tienen más de 30 observaciones, asumiremos normalidad, de todos modos.

Evaluamos la Homocedasticidad para cada métrica con el test de Levene (8 pruebas en total, una por cada métrica). De los 8, 6 cumplen la condición. Para los dos que no, empelamos la corrección de Welch en la prueba T-Student.

Finalmente, a tener en cuenta, nuestra hipótesis nula y alternativa.


- H0: Métrica(i) Eurocopa < Métrica Copa América
- H1: Métrica(i) Eurocopa > Métrica Copa América

  <img width="728" alt="image" src="https://github.com/user-attachments/assets/00f13de7-bcfd-4df7-9ca4-ac1a788656a5">

