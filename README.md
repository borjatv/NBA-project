# NBA-project

## Resumen

El objetivo de este proyecto es determinar si el salario de un jugador de la NBA se corresponde con su rendimiento en la pista teniendo en cuenta la evolución que ha tenido el juego desde 2001.

Para ello, se han tomado estadísticas de las intervenciones de todos los jugadores en sus partidos de temporada regular desde 2001, así como otros datos relevantes por temporada como el salario, la altura, el peso o las posiciones en las que el jugador actúa.

Con estos datos se ha aplicado clustering para identificar grupos de jugadores en función de sus características y se ha creado un modelo que evaluará las características del jugador en la última temporada y predecirá el salario que debería estar cobrando, el cual se podrá comparar con el que realmente tiene y determinar si el jugador está sobrevalorado o infravalorado.

El material para presentar este proyecto está en formato ```.pdf```en esta misma carpeta con el nombre **```NBA project presentation.pdf```**.

---

## Contenido
- [Dataset](#dataset)
- [Notebooks](#notebooks)
- [Visualizaciones](#visualizaciones)
- [Conclusiones](#conclusiones)

## Dataset

Se han usado 3 datasets para este proyecto:

### **NBA Regular Season Data 1950-2021**

* **Fuente:** Kaggle
* **Link:** [https://www.kaggle.com/amanuelwk/nba-regular-season-data](https://www.kaggle.com/amanuelwk/nba-regular-season-data)
* **Contenido:** Estadísticas de las intervenciones de todos los jugadores en sus partidos de temporada regular desde 1950 hasta 2021: puntos, rebotes, asistencias, titular/suplente, minutos jugados, tiros anotados/intentados, etc.
* **Archivos:** ```season_XXXX_detailed.csv```, donde XXXX es la temporada ya que hay un archivo por temporada.

### **NBA Salaries**

* **Fuente:** data.world
* **Link:** [https://data.world/datadavis/nba-salaries](https://data.world/datadavis/nba-salaries)
* **Contenido:** Información scrapeada de [Basketball-Reference](https://www.basketball-reference.com/) de los jugadores de la NBA desde 1985 hasta 2018: salario, peso, altura, posiciones en las que pueden actuar, etc.
* **Archivos:** ```salaries.csv```, con la información que necesitamos pero con alias en lugar de los nombres completos de los jugadores y ```players.csv```, con las equivalencias de estos alias con los nombres completos de los jugadores.

### **Dataset propio**
* **Fuente:** Web scraping de [Basketball-Reference](https://www.basketball-reference.com/)
* **Link:** [https://data.world/datadavis/nba-salaries](https://data.world/datadavis/nba-salaries)
* **Contenido:** Información scrapeada de [Basketball-Reference](https://www.basketball-reference.com/) con Selenium Webdriver que contiene la misma información que el dataset *NBA Salaries* para los años que faltaban, es decir, desde 2019 hasta 2021.
* **Archivos:** ```data_nba.csv```, con la información ya concatenada con los datasets anteriores, es decir, desde 2001 hasta 2021.

## Notebooks

El código desarrollado para llegar al modelo se ha dividido en 3 notebooks.

### **```NBA 01 data consolidation & web scraping.ipynb```**

En este notebook se han consolidado los 3 datasets en uno solo, que se ha exportado como ```data_nba.csv```. Este archivo contiene todas las observaciones del dataset *NBA Regular Season Data 1950-2021* filtradas desde 2001, y a cada observación se le ha añadido la información correspondiente de cada jugador proveniente de los otros 2 datasets *NBA Salaries* y *Dataset propio*:el salario que cobraba en el año de la observación, las posiciones en las que actúa, si es zurdo o diestro, el peso o la altura.

En primer lugar, se han unido los archivos ```salaries.csv``` y ```players.csv``` del dataset *NBA Salaries* y se han añadido a las observaciones del dataset *NBA Regular Season Data 1950-2021*. Para ejecutar esta tarea de forma automática se ha utilizado la librería **fuzzywuzzy**, ya que había alguna diferencia en los nombres de los jugadores entre ambos datasets.

A continuación, se ha generado el *Dataset propio* haciendo web scraping de [Basketball-Reference](https://www.basketball-reference.com/) con **Selenium Webdriver**.

Tras llevar a cabo algunas operaciones de limpieza de datos, se han exportado los datos consolidados al archivo ```data_nba.csv```.

### **```NBA 02 data cleansing - EDA.ipynb```**

En este notebook se han llevado a cabo una serie de operaciones de limpieza y transformación de variables a los datos del archivo previamente generado ```data_nba.csv```, el cual se ha importado y exportado con el mismo nombre para sobreescribirlo.

### **```NBA 03 data wrangling, visualization & model.ipynb```**

En este notebook están explicados todos los pasos que se han llevado a cabo todo el código generado con el objetivo de:

* Aplicar clustering para identificar grupos de jugadores en función de sus características, que se corresponderán con sus posiciones.
* Generar archivos _.csv_ para exportar a **Tableau** y crear visualizaciones.
* Generar visualizaciones directamente en el notebook.
* Crear un modelo que nos permita saber si un jugador actual está sobrevalorado o infravalorado.

Para facilitar las visualizaciones y poder representar el rendimiento en un eje (el salario estará en otro), se ha creado una variable 'valoración' ```VAL```con un criterio similar al que se utiliza en otras ligas como la EuroLeague o la ACB y que consiste en sumar las aportaciones (puntos, rebotes, asistencias, tapones, robos y recuperaciones) y restar las penalizaciones (tiros fallados).

Respecto al modelo principal, como hay cierta diferencia entre los scores con los datos de train y test, puede haber un impacto a la hora de predecir jugadores ya existentes, que será el caso ya que no se va a predecir el salario para un jugador para el cual no tenemos las estadísticas. Es por eso que la predicción es posible que sea algo más conservadora si el jugador está en _X_train_ y más agresiva si está en _X_test_.

Para que se traten de igual forma todos los jugadores de 2021, que son los que queremos usar ya que el objetivo es realizar la predicción con los jugadores actuales, se ha creado un segundo modelo adicional eliminando dichas observaciones del modelo de forma que éstos no estén incluidos en el _X_train_. El score ha bajado y las predicciones de salario difieren un poco más de los salarios actuales, es decir, el modelo es menos conservador.

## Visualizaciones

Del notebook ```NBA 03 data wrangling, visualization & model.ipynb``` se han exportado 2 archivos _.csv_ para generar sendas visualizaciones en **Tableau**.

### **[NBA 2001-2021 players per salary and performance](https://public.tableau.com/views/NBAplayers_16276448378390/Playerspersalaryandperformance?:language=es-ES&publish=yes&:display_count=n&:origin=viz_share_link)**

Evolución del baloncesto en la NBA desde 2001 hasta 2021.

Los datos están en el archivo el archivo ```gamestats.csv```, generado en el notebook ```NBA 03 data wrangling, visualization & model.ipyn```.

### **[NBA game stats evolution 2001-2021](https://public.tableau.com/views/NBAplayers_16276448378390/Playerspersalaryandperformance?:language=es-ES&publish=yes&:display_count=n&:origin=viz_share_link)**

Jugadores de la NBA desde 2001 hasta 2021 por salario y rendimiento.

En este scatterplot, cada punto es un jugador con sus datos medios por temporada.

*NOTA*: si se filtran varias temporadas, habrá jugadores repetidos ya que cada punto representa a un jugador en una temporada.

Se representan las siguientes variables:

- Eje x: salario
- Eje y: valoración (variable creada en el notebook X para resumir el rendimiento de los jugadores por partido en una variable)
- Color: posición en función del clustering
- Tamaño: minutos jugados en la temporada

Además, se pueden aplicar los siguientes filtros:

- Temporada
- Edad
- Equipo
- Posición en función del clustering
- Posición en función de la información obtenida en los datasets *NBA Salaries* y *Dataset propio*. En este caso son 5 filtros, 1 por posición, ya que hay jugadores polivalentes que pueden actuar en varias posiciones.

*NOTA*: al tener los filtros de las posiciones obtenidas de los datasets y del clustering, podemos comprobar cómo efectivamente el clustering ha separado a los jugadores en grupos que coinciden bastante con sus posiciones.

Los datos están en el archivo el archivo ```data_exported_pg.csv```, generado en el notebook ```NBA 03 data wrangling, visualization & model.ipyn```.

## Conclusiones

Con la visualización generada en **Tableau** y la referencia de la regresión lineal sencilla en función de los filtros que apliquemos, es bastante intuitivo poder identificar a los jugadores que están rindiendo por encima o por debajo de lo esperado en función de su salario.

De todas formas, para poder obtener una predicción de forma más precisa, se han generado 2 modelos con 2 enfoques distintos, uno más conservador y otro más agresivo, con lo que ejecutando ambos para observar una predicción más conservadora y otra más agresiva, y combinándolos con las visualizaciones de datos creadas en **Tableau**, se podrá al menos conocer el rango en el que debería oscilar el salario de un jugador en función de su rendimiento en la pista.

La conclusión que podemos extraer es que, si bien es muy preciso decidir si un jugador está sobrepagado o no (ambos modelos se ponen de acuerdo en si la diferencia entre el salario predecido y el real es positiva el 90% de los casos), puede resultar algo más complicado definir un salario en función de las variables que tenemos ya que hay bastante error entre ellas y las franquicias no sólo no siempre aciertan ajustando dichos salarios, sino que entran otros temas. No solamente el error de las franquicias, sino también otras variables intangibles como podría ser el liderazgo de un jugador en el equipo o el carisma, además de otros temas de negocio. Por ejemplo, si un jugador hace un buen año y consigue un buen contrato para los próximos X años pero baja el rendimiento en estos años, se generará bastante ruido en el modelo.
