# EDA-fifa

### Introducción
El objetivo de este trabajo es comprobar qué tan reales son los puntajes otorgados en el videojuego de fútbol “FIFA 23”. Trataremos de encontrar en el set de datos el equipo ideal consistiendo de 11 jugadores profesionales. Luego, podremos comparar el resultado obtenido con el Equipo ideal del Mundial de Qatar 2022. Podremos observar si el equipo coincide, o cuántos de los jugadores se encuentran en este. Veremos que tanto se asemeja la base de datos del juego respecto a lo que ocurre en el torneo más importante del fútbol y evento deportivo más visto del mundo.

### Importamos el archivo con el dataset completo que sacamos de Kaggle. 
Para hacer este paso tuvimos que crear la variable "path" y, a su vez, importamos las librerías correspondientes que se ven en la segunda parte del código. 

  ```python

  import os
  dirname = os.path.dirname(os.getcwd())
  path = os.path.join(dirname, 'FIFA-13-EDA---Constenla---Depascuali/media/fifa_23_280922.csv')
  os.getcwd()


  import pandas as pd
  import seaborn as sns
  import matplotlib.pyplot as plt
  import numpy as np
  df = pd.read_csv(path)
  df.describe
  ```

### Chequeo de los tipos de datos de cada columna.
Hay que corroborar que los tipos de datos de las columnas sean los adecuados. En este caso eran todos correctos, entonces no hubo que corregirlos.

```python
 
df.dtypes
 ```
 
### Nos aseguramos de que todas las columnas tengan valores completos en cada fila.
Como sabemos la cantidad de filas que hay, contamos la cantidad de valores completos de cada columna. Con esto comprobamos que todas las celdas estaban completas con la información pertinente.
 
```python
 
df.count()
 ```
 
### Eliminación de columnas que no contribuyen al análisis.
Para nuestro análisis decidimos no utilizar las columnas "Id" y "Salario". Esto se debe a que el "Id" no es útil para lograr "insights" y consideramos que el "Salario" es información que puede llegar a ser falsa ya que es información confidencial de los clubes.
 
```python
 
df.drop(['Id', 'Salaire'], axis=1, inplace=True)
```
 
### Traducción del nombre de las columnas
El nombre de las columnas están en francés, por lo tanto buscamos la traducción y las renombramos en castellano. También imprimimos las primeras cinco filas para ver si esto había quedado bien. 
 
```python
 
df = df.rename(columns={"Nom":"Nombre","Général":"General","Potentiel":"Potencial","Pays":"Pais","Bon pied":"Pierna buena","Mauvais pied":"Pierna mala","Gestes techniques":"Gestos tecnicos","Taille":"Altura","Rendement offensif":"Rendimiento ofensivo","Rendement defensif":"Rendimiento defensivo","Valeur":"Valor","Centres":"Centros","Finition":"Definicion","Precision tete":"Precision de cabeza","Passes courtes":"Pase corto","Volee":"Volea","Dribbles":"Dribbling","Effet":"Efecto","PCF":"Precision tiros libres","Passes longues":"Pase largo","Controle":"Control","Acceleration":"Acceleracion","Vitesse": "Velocidad", "Agilite": "Agilidad", "Reactivite": "Reactividad", "Equilibre": "Equilibrio", "Puissance frappe": "Potencia", "Detente": "Recuperacion", "Endurance": "Resistencia", "Force": "Fuerza", "Tirs de loin": "Disparo desde lejos", "Agressivite": "Agresividad", "Interceptions": "Intercepciones", "Placement": "Posicionamiento", "Vista": "Visión", "Penalty": "Penales", "Calme": "Calma", "Conscience defensive": "Consciencia defensiva", "Tacle debout": "Entradas","Tacle glisse": "Entradas por el suelo", "Plongeon": "Tapadas", "Jeu main": "Juego de mano", "Jeu pied": "Juego con el pie", "Placement.1": "Posicionamiento del arquero", "Reflexes": "Reflejos"})
df.head(5)
```
 
### Comenzamos el análisis de datos.
Para realizar el análisis y entender cómo estaba compuesta la muestra, decidimos ver qué país contaba con la mayor cantidad de jugadores.
 
```python
 
top = df['Pais'].value_counts().head(10)
top
```
 
### Desplegamos dicha información en un gráfico.
Aquí el gráfico mostrará los países con mayor cantidad de jugadores en el FIFA 23.
 
 ```python
 
fig,ax = plt.subplots(figsize = (10,8))
ar = top.values
ra = top.index
ax.bar(ra,ar, color = 'green')
ax.set_title(('Top 10 paises con mayor cantidad de jugadores'))
plt.show()
```
 
### Realizamos un gráfico 
Este muestra cómo está compuesto el ranking de los 100 mejores jugadores según los países. Consideramos que era información interesante y nutre el análisis.
 
```python
 
plt.figure(figsize=(16,6),dpi=150)
sns.countplot(x='Pais', data=df.head(100),palette='CMRmap')
plt.xticks(rotation=90)
plt.xlabel('Pais')
plt.ylabel('Cantidad')
plt.title('Top 100 jugadores por pais', fontsize=20)
plt.show()
```
 
### Creamos 4 columnas nuevas (Ataque, Mediocampo, Defensa y Arquero)
Más relacionado al análisis, decidimos agrupar los aspectos ofensivos en la columna “Ataque”, los correspondientes a mediocampistas en “Mediocampo”, los defensivos en “Defensa” y por último los de arquero el “Arquero”. Esto lo hicimos con el fin de saber que tán buenos son los jugadores en esas 4 posiciones.
 
```python
 
Ataque:
df["Ataque"] = ((df["Definicion"] + df["Volea"] + df["Dribbling"] + df["Precision tiros libres"] + df["Efecto"] + df["Acceleracion"] + df["Control"] + df["Velocidad"] + df["Agilidad"])  /9)
 
df.head(5)
 
Mediocampo:
df["Mediocampo"] = ((df["Pase corto"]+ df["Centros"] + df["Dribbling"] + df["Precision tiros libres"] + df["Control"] + df["Pase largo"] + df["Velocidad"] + df["Equilibrio"] + df["Resistencia"] + df["Visión"] + df["Posicionamiento"] + df["Disparo desde lejos"] + df["Recuperacion"] + df["Intercepciones"] + df["Consciencia defensiva"]) /15)
 
df.head(5)
 
Defensa:
df["Defensa"] = (( df["Precision de cabeza"]+ df["Fuerza"] + df["Agresividad"] + df["Intercepciones"] + df["Consciencia defensiva"] + df["Entradas"] + df["Entradas por el suelo"]) /7)
 
df.head(5)
 
Arquero:
df["Arquero"] = (( df["Tapadas"]+ df["Juego de mano"] + df["Juego con el pie"] + df["Posicionamiento del arquero"] + df["Reflejos"] ) /5)
 
df.head(5)
```
 
### Imprimimos el "insight" más importante de este EDA 
Finalmente, encontramos el equipo ideal según los aspectos del FIFA. Esto lo hicimos, imprimiendo el mejor arquero, los mejores 3 defensores, los mejores 4 mediocampistas y 4 delanteros. Para determinar cuáles son los mejores utilizamos las 4 columnas creadas.
 
```python
 
Arquero: 
df.sort_values(by="Arquero",inplace=True,ascending=False)
df.head(1)
 
Defensa: 
df.sort_values(by="Defensa",inplace=True,ascending=False)
df.head(3)
 
Mediocampo:
df.sort_values(by="Mediocampo",inplace=True,ascending=False)
df.head(4)
 
Ataque:
df.sort_values(by="Ataque",inplace=True,ascending=False)
df.head(3)
```
 
### Conclusión
En conclusión, el problema que buscamos solucionar a lo largo de este EDA es saber que tán precisos son los puntajes del fifa con respecto a los rendimientos de los jugadores. Para esto, analizaremos posteriormente del mundial, si los 11 jugadores que seleccionamos fueron protagonistas de dicho evento deportivo. Además, la FIFA una vez finalizado el mundial publicará el once ideal del mundial, si algunos de esos jugadores coinciden con nuestro 11 ideal del EDA se podrá calcular qué tan reales son los puntajes que pone el juego FIFA 23. 
