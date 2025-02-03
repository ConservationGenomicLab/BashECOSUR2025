# Explorando el contenido de archivos de secuenciación masiva, el formato fastq
----
En esta sección vamos a utilizar los comandos de bash y habilidades que ya hemos visto en el curso.

Los objetivos de esta sección son:

> -**Explorar la estructura de un archivo _fastq_ usando comandos básicos.**

> -**Obtener información básica de un archivo _fastq_.**

> -**Extraer las secuencias de un archivo _fastq_.**

> -**Identificar elemetos como barcodes o sitios de restricción en las lecturas.**


### Planteamiento

Usaremos un juego de datos real. Al anlalizar lo datos detectamos una pérdida de información del 22%,cuando normalmente esperamos una pérdida de alrededor del 8% de datos. Usaremos lo aprendido durante el curso para detectar fuentes de error posiblemente asociadas a factores técnicos de la unidad de secuenciación.

En este caso tenemos un fastq generado para 96 muestras de "alguna especie" (más adelante veremos como podemos identificar a que organismo pertenecen) por medio de la técnica de RadSeq con la enzima de restricción _ApeKI_.

Los sitios de corte de la enzima son **GAGC** y **GTGC**.


Los datos constan del archivo _fastq.gz_ y  del archivo con los índices de cada individuo _barcodes.txt_.

Nota que los datos fueron generados con un submuestreo al azar del archivo original para fines del ejercicio.


### Parte I


1. **Vamos a identificar los elementos de un archivo fastq.gz.**

>> Usaremos **```zcat```**, que es una variante de **```cat```** que funciona para archivos comprimidos.

>> Usa el pipe **|** junto con **```wc -l```** para conocer cuantas líneas contiene el archivo.

>> Ahora usa **```head```** para observar las primeras líneas del archivo.

>> Usa **```grep -c```** para determinar el número de lecturas. Busca un **_"string"_** adecuado para este fin.

 **Tip:** recuerda usar <kbd> ctrl + C </kbd> para cortar ó cancelar un proceso.

#### Preguntas:
+ ¿Cuántas lecturas tiene el archivo?
+ ¿Cuántas líneas componen una lectura fastq?

### Parte II
1. **Extraer las secuencias nucleotídicas de un archivo fastq**

Vamos a "aislar" las secuencias del formato fastq: **Tip:** Siempre usa una fracción de los datos antes de aplicar tus líneas de comando (_i.e._ usa **```head```** ).

>> Aisla los encabezados de las secuencias con **```grep```**.

>> Utiliza la opción **-A 1** para obtener la línea con las secuencias.

>> Puedes usar **```grep -v```** para eliminar líneas no deseadas. **Tip:** agrega la opición ```--no-group-separator```.

>> Almacena tus secuencias en un archivo (usa **>** para direccionar la salida).

#### Preguntas:
+ ¿Cuántas secuencias obtuviste?
+ ¿Puedes estimar cuántas secuencias tienen bases indeterminadas (_i.e._ **N**'s )?
### Parte III
1. **Busquemos ahora los barcodes que identifican a los individuos y los sitios de restricción.**

>> Como vimos anteriormente, los barcodes y los sitios de corte se encuentran en las primeras bases nucleotídicas. Vamos a extraer los primeros 15 sitios. Usa **```cut -b```**. No olvides crear un archivo con estas bases nucleotídicas.

>> Revisa el contenido del archivo _barcodes.txt_. Por ahora sólo nos interesa la columna con las secuencias, extrae esta columna con **```cut```** y genera un nuevo archivo.

>> Ahora busquemos las secuencias de los barcodes en nuestro archivo que generamos en el paso anterior. Prueba usando la opción **```-f```** seguido del nombre del archivo.

>> Estima cuántas secuencias tienen alguno de los barcodes.

>> Ahora estima cuántas tienen los sitios de restricción.

#### Preguntas:
+ ¿Cuántas secuencias tienen tanto los barcodes como los sitios de restricción?
+ ¿Del total de lecturas, qué porcentaje se perdieron por no tener los barcodes o los sitios de restricción?

---
### Notas finales:

El _fastq_ que trabajamos representa un caso real de errores en la preparación de las bibliotecas por parte de la unidad de secuenciación. Genearlmente este tipo de errores no están asociados a la calidad del tejido o pureza de la extracción.

Estos resultados nos permiten tomar desiciones para decidir por parámetros y estrategias para obtnener la mayor cantidad de marcadores.

Considera que en bash hay varias maneras de obtener un resultado, en este caso el ejercicio tiene un objetivo demoastrativo y con ello no suponemos que es la única manera de hacerlo.

En nuestra experiencia siempre recomendamos revisar los archivos "crudos" antes de usar algún programa. Esto nos da señales de la calidad de la secuenciación y posiblesproblemas asociados.

