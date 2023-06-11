### Descargar y leer el archivo

```
# Primero importamos las librerias necesarias para el proyecto
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# Importamos la base de datos y creamos un dataframe llamado "indice"
indice = pd.read_csv('/content/IMM_2020csv.csv')
```
### Datos estadisticos generales del dataframe
```
# Mostramos algunos datos estadisticos generales del dataframe
indice.describe(include = 'all')
 
# Podemos observar que se encuentran los 32 estados del pais y que se
# se consideran 2,328 municipios en todo el pais.
# En terminos generales, podemos destacar que la media de analfabetismo por
# municipio en el pais es de 10.16 con una desviacion estandar de 7.6. Esto
# puede sugerir una sectorización del analfabetismo.
```
### Porcentaje de grado de marginacion por estado

```
# Ahora hacemos un grafico del porcentaje de municipios con los diferentes niveles de indice de marginacion

# Primero hacemos un nuevo dataframe que incluya solo los valores de interes.
marg_por_estado = indice.loc[:, ['NOM_ENT', 'NOM_MUN', 'GM_2020']]
marg_por_estado

# Calculamos los porcentajes de cada grado de marginación por estado
porcentajes = marg_por_estado.groupby('NOM_ENT')['GM_2020'].value_counts(normalize=True).mul(100).unstack()
porcentajes

# Graficamos
ax = porcentajes.plot(kind='bar', stacked=True, figsize=(12, 6))
ax.legend(title='Grado de Marginación', bbox_to_anchor=(1, 1))
ax.set_xlabel('Estado')
ax.set_ylabel('Porcentaje de Grado de Marginación')
ax.set_title('Porcentaje de Grado de Marginación por Estado')

# Ajustamos las etiquetas del eje x para mostrar solo los nombres de los estados
x_labels = porcentajes.index
ax.set_xticklabels(x_labels, rotation=90)

# Guardamos la grafica en .png y la mostramos
plt.tight_layout()
plt.savefig('Porcentaje de grado de marginacion por estado.png', dpi=300, bbox_inches='tight')
plt.show()
```

![download](https://github.com/julioelias-o/Indice-de-marginalizacion/assets/134743799/838ce248-bd5a-4fb8-819c-b3d886f8e5c4)

Porcentaje de poblacion por estado y grado de marginacion
```
# Primero hacemos un nuevo dataframe que incluya solo los valores de interes.
poblacion = indice.loc[:, ['POB_TOT', 'NOM_ENT', 'NOM_MUN', 'GM_2020']]
poblacion

# Calculamos el porcentaje de población por estado y grado de marginación
poblacion['porcentaje_poblacion'] = poblacion['POB_TOT'] / poblacion.groupby('NOM_ENT')['POB_TOT'].transform('sum')
porcentaje_marginacion = poblacion.groupby(['NOM_ENT', 'GM_2020'])['porcentaje_poblacion'].sum().unstack()

# Graficamos
fig, ax = plt.subplots(figsize=(12, 4))
porcentaje_marginacion.plot(kind='bar', stacked=True, ax=ax)

# Etiquetamos
ax.set_xlabel('Estado')
ax.set_ylabel('Porcentaje de población')
ax.set_title('Porcentaje de población por estado y grado de marginación')
ax.legend(title='Grado de marginación', bbox_to_anchor=(1, 1), loc='upper left')

# Guardamos la grafica en .jpg y la mostramos
plt.savefig('Porcentaje de poblacion por estado y grado de marginacion.jpg', dpi=300, bbox_inches='tight')
plt.show()
```

![download](https://github.com/julioelias-o/Indice-de-marginalizacion/assets/134743799/b89f7780-2dec-437f-acd1-dcd3953ebe3e)



### ¿Hay coincidencias entra la gráficas anteriores?  ¿Algún hallazgo?

Al comparar las graficas, se observa que aunque un estado puede tener distintos grados de marginación,
la población con menor grado de marginación tiene mayor fuerza sobre la población total.
Esto se puede traducir como que vive más gente en los municipios con menor grado de marginación.
Usualmente las ciudades son las que tienen una mayor población en un estado, otros municipios
rurales tiene una menor concentración de gente.

Se puede concluir que las zonas rurales del pais son las que tienen un mayor grado de marginación.

### Relación entre analfabetismo y poblaciones en localidades pequeñas
```
# Para graficar la relación de porcentaje de analfabetismo respecto al porcentaje de poblaciones en localidades de menos de 5,000 habitantes.

# Hacemos una grafica de puntos
plt.plot(indice['PL.5000'], indice['ANALF'], 'o')

# Etiquetamos
plt.title('Relación entre analfabetismo y poblaciones en localidades pequeñas')
plt.xlabel('Porcentaje de poblaciones en localidades de menos de 5,000 habitantes')
plt.ylabel('Porcentaje de analfabetismo')

plt.show()
```

![download](https://github.com/julioelias-o/Indice-de-marginalizacion/assets/134743799/0ae75805-2a13-405f-893e-3e01f1ee59e5)

### ¿Existe una relación? ¿Cómo podrías analizar con que variable tiene más correlación el porcentaje de analfabetismo en personas mayores de 15 años?

```
# Parece que existe una muy leve relación entre estos dos indices, puede observarse un ligero incremento
# en la disperción de los puntos conforme aumenta el porcentaje de poblaciones pequeñas.
# Hay municipios pequeños que su totalidad de población entra en este indice de poblaciones pequeñas,
# pero esto no orienta directamente a que hay más analfabetismo solo por ser pequeñas como
# se observa en la linea vertical de la derecha.
```

```
# Para encontrar una variable que tenga más conexion con el indice de analfabetismo,
# es necesario graficar de la misma manera la relación con las otras variantes de los indices de marginación.

# Se encontro que el porcentaje de población de 15 años o más sin educación básica
# se relaciona más con el grado de analfabetismo, relación que es bastante logica.
plt.plot(indice['SBASC'], indice['ANALF'], 'o')
plt.title('Relación entre analfabetismo y población sin estudios')
plt.xlabel('Porcentaje de poblaciones sin educación basica')
plt.ylabel('Porcentaje de analfabetismo')

plt.show()
```
![download](https://github.com/julioelias-o/Indice-de-marginalizacion/assets/134743799/5a079c68-33a8-4776-9f72-4bf781932f0e)

### Indicador

```
# Un indicador interesante podria ser si existe relación entre el acceso a la energia electrica
# y el acceso a tuberias de agua. Esto puede demostrar si hay correlación entre estas
# dos variables.

# Obtenemos el nuevo dataframe con estas dos variables
indicador = indice.loc[:, ['OVSEE', 'OVSAE']]
indicador

# Lo guardamos en formato parquet
indicador.to_parquet(r'C:\Users\Julio\Downloads\indicador.parquet')

# Graficamos las dos variables
plt.scatter(indice['OVSEE'], indice['OVSAE'], alpha=0.3)

# Etiquetamos
plt.xlabel('Viviendas sin acceso a energía eléctrica')
plt.ylabel('Viviendas sin tuberías de agua potable')

# Título del gráfico
plt.title('Relación entre acceso a energía eléctrica y agua potable')

# Mostrar el gráfico
plt.show()
```
![download](https://github.com/julioelias-o/Indice-de-marginalizacion/assets/134743799/82be30af-52c4-44b8-a818-57f984ab7957)

No parece haber una relación entre estas dos variables, por lo que no se puede tomar por hecho que si no tienen acceso a energia electrica tampoco tendran acceso a agua potable.


