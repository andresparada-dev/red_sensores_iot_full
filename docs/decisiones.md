# Decisiones de diseño — Semana 3

## 1. Punto de entrada

El proyecto mantiene un único punto de entrada:

```text
IngestaSensores.main()
```

No se crean aplicaciones independientes por semana.

`BancoDePruebas` es una clase auxiliar y no contiene `main`.

## 2. Búsqueda por timestamp

Se utilizan dos estrategias:

- Búsqueda lineal: no requiere ordenamiento.
- Búsqueda binaria: requiere que el arreglo esté ordenado por timestamp.

Los datos sintéticos de `GeneradorDatos` se generan en orden cronológico, por lo que la búsqueda binaria por timestamp cumple su precondición.

## 3. Búsqueda por PM2.5

No se asume que los datos estén ordenados por PM2.5.

Por tanto, la búsqueda binaria por PM2.5 se conserva como experimento para demostrar el efecto de una precondición incumplida.

## 4. Comparación de String

Los identificadores de estación se comparan mediante:

```java
equals()
```

y no mediante:

```java
==
```

porque se necesita comparar contenido.

## 5. Medición

La comparación principal entre algoritmos utiliza el número de comparaciones.

El tiempo en milisegundos se conserva como evidencia experimental, pero no es la única medida utilizada.

## 6. Evolución del proyecto

La Semana 3 agrega una nueva capacidad a la misma plataforma:

```text
Sensores
   ↓
Ingesta
   ↓
Repositorio
   ↓
Búsqueda
   ↓
Medición de eficiencia
```

La Semana 4 podrá extender esta misma arquitectura para estudiar ordenamiento.


---

# Semana 5 — La cadena

## 1. Experimentos de BancoDeListas

Fui activando los experimentos por fases, quitando el comentario de cada uno en el `main()` de `BancoDeListas`, y estos fueron mis resultados.
### Experimento 1:

```text
Arreglo          tamano: 5   primera: 0000000000
Lista enlazada   tamano: 5   primera: 0000000000
```

### Experimento 2:

```text
Antes de eliminar:          tamano() = 10   nodos reales = 10
Después de eliminar la 5:   tamano() = 9    nodos reales = 9
```

### Experimento 3: insertar al inicio

| Lecturas | Arreglo: movimientos | Arreglo: ms | Lista: saltos | Lista: ms |
|---------:|---------------------:|------------:|--------------:|----------:|
| 10.000   | 49.995.000           | 270         | 0             | 1         |
| 50.000   | 1.249.975.000        | 2.721       | 0             | 7         |
| 100.000  | 4.999.950.000        | 11.326      | 0             | 1         |

### Experimento 4: recorrer por posición

| Lecturas | Arreglo: accesos | Arreglo: ms | Lista: saltos | Lista: ms |
|---------:|-----------------:|------------:|--------------:|----------:|
| 1.000    | 1.000            | 0           | 499.500       | 3         |
| 10.000   | 10.000           | 0           | 49.995.000    | 104       |
| 50.000   | 50.000           | 1           | 1.249.975.000 | 2.316     |

### Experimento 5: búsqueda binaria sobre una cadena

```text
Buscando la última lectura de 50.000:
   Búsqueda LINEAL  -> posición 49999, saltos: 50.000
   Búsqueda BINARIA -> posición 49999, saltos: 749.990
```

## 2. Pruebas de eliminar()

Probé eliminar en los casos que pide la guía y comparé `tamano()` con `contarRecorriendo()` después de cada operación:

| Caso | Resultado |
|------|-----------|
| Eliminar el primero | El segundo pasó a ser el primero |
| Eliminar el último | Se actualizó `ultimo`, y al agregar otro al final quedó en orden |
| Eliminar uno del medio | Solo desapareció ese, los siguientes siguieron ahí |
| Eliminar el único elemento | La lista quedó vacía |
| Eliminar en lista vacía o con índice inválido | Devolvió `false` sin error |

En todos los casos `tamano()` dio lo mismo que `contarRecorriendo()`.

## 3. Bitácora

**1. ¿Por qué insertar al inicio es más económico en una lista enlazada que en un arreglo?**

Porque en el arreglo tiene que empezar a mover todos los datos hacia la derecha para dejar un espacio, mientras que en la lista enlazada solo crea un nodo y lo conecta, o sea, le apunta al que era el primero. Es más barato porque no tiene que mover todos los datos.

**2. ¿Por qué el acceso a una posición arbitraria es más costoso en una lista?**

Porque en la lista tiene que recorrer los nodos uno a uno, mientras que en un arreglo va directamente a la posición que le indica.

**3. ¿Qué diferencia existe entre un experimento de rendimiento y una prueba unitaria?**

Un experimento de rendimiento analiza los tiempos, el gasto y los datos de medición de cómo fue la ejecución, mientras que una prueba unitaria se refiere a si pasó o si falló el código que se estaba probando. Creo que esto es más para los métodos que se usan.

**4. ¿Qué caso límite consideras más importante probar en eliminar() y por qué?**

El caso más importante es eliminar un nodo del medio, porque si en vez de conectar el nodo anterior con el siguiente se pone `null`, se pierden todos los nodos que venían después. Pueden desaparecer varias lecturas sin ningún error, y `tamano()` deja de coincidir con los nodos reales.

**5. Para el histórico de lecturas de sensores, ¿qué estructura elegirías si predominan las inserciones al inicio? ¿Y si predominan las consultas por posición?**

Si todo se basa en inserciones al inicio, sale mucho mejor la lista enlazada, porque solo sería ir creando nodos nuevos al principio y enlazándolos con el que era el primero. Pero si predominan las consultas por posición, sería mejor el arreglo, ya que va directamente a la posición y se ahorra mucho en la búsqueda por posición arbitraria.

