# El uso de _awk_ para la edición y manipulación de secuencias en formato fasta
---
Ahora vamos revisar lasaplicaciones de **_awk_** para editar secuencias fasta.

**_awk_** es un lenguaje con una sintaxis particular. No es el objetivo de la presente práctica se un manual o guía de awk, más bien queremosmostrar las apliaciones para resolver problemas comunes cuando trabajamos con secuencias. En particular, **_awk_** es muy eficiente cuando trabajamos con grandes volúmenes de secuencias.

## Parte I

Vamos a generar un archivo fasta a partir de las secuencias que obtuvimos en el ejercicio anterior:


Como siempre, empieza con una fracción de los datos. Suponiendo que el archivo con las secuencias que generaste anteriormente se llama _Secuencias.txt_ :

```bash
    head Secuencias.txt | awk '{print ">seq_" ++count ORS $0}'
```

Observa el comando, identifica que hace cada elemento, y xuando estés conforme genera tu archivo completo en formato fasta.

### Preguntas
+ ¿Cómo pondrías otro nombre al prefijo al nombre de las secuencias?

## Parte II

El tamaño en número de bases muy importante cuando trabajamos secuencias. Obtengamos un estimado del largo de las secuencias:

```bash
    awk '/^>/{if (l!="") print l; print; l=0; next}{l+=length($0)}END{print l}' Secuencias.fasta
```

Vamos un poco más allá y veamos como se distribuyen los tamaños. Para esto vamos a usar **R**. Primero obtén la lista los conteos únicamente, prueba usando **```grep```** ó **```sed```**.

```bash
R
```
Dentro de **R**, vas a observar un mensaje con la versión y promt **>**.

```R
#Suponeindo que el archivo con los tamaños se llama seq_sizes.txt vamos a cargar los datos.
seq_sizes <- read.table(file="seq_sizes.txt")
#un resúmen estadístico de los valores.
summary(seq_sizes)
#vamos agenerar un gráfico
png("Sizes.png")
hist(seq_sizes$V1,col="red4",border="white",xlab="Tamaño del fragmento")
dev.off()
#listo, ahora salgamos de R
q(save=no)
```

### Preguntas
+ **R** es un lenguaje distinto a **bash**, ¿Puédes identificar dos diferencias fundamentales en sus sintaxis?

De regreso en **bash** copia el gráfico a tu computadora.

Vamos a filtrar por tamaño, digamos como mínimo 120pb:

```bash
awk -v RS='>[^\n]+\n' 'length() >= 120 {printf "%s", prt $0} {prt = RT}' Secuencias.fasta
```

### Preguntas
+ Escoge distintos tamaños y observa como cambia el número de secuencias recuperadas.

Podemos ordenar las secuencias por tamaño:

```bash
awk '/^>/ {printf("%s%s\t",(N>0?"\n":""), $0);N++;next;} {printf("%s",$0);} END {printf("\n");}' Secuencias.fasta |\
awk -F '\t' '{printf("%d\t%s\n",length($2),$0)}' |\
sort -k1,1n | cut -f 2- |tr "\t" "\n" > Secuencias_size.fasta
```

## Parte III

Como lo prometimos en el ejercicio anterior, vamos a tratar de identificar a que organismo pertenecen las secuencias.

Considera que tenemos muchas secuencias. Esto puede causar que la búsqueda sea muy tardada, vamos a escoger las primeras secuencias:

```bash
    awk '/^>/ {n++} n>5 {exit} {print}' Secuencias.fasta
```

Si quisiéramos un muestreo al azar podemos usar:

```bash
    cat Secuencias.fasta |\
    awk '/^>/ { if(i>0) printf("\n"); i++; printf("%s\t",$0); next;} {printf("%s",$0);} END { printf("\n");}' |\
    shuf |\
    head -n 5 |\
    awk 'BEGIN{FS="\t"}{printf("%s\n%s\n",$1,$2)}'
```
Ok, se ve complicado pero de hecho es muy eficiente.


Selecciona _diez_ secuencias y hagamos la búsqueda en NCBI. Supongamos que el archivo que generaste con _diez_ secuencias se llama _Secuencias_mini.fasta_.

```bash
blastn -db nt -query Secuencias_mini.fasta -remote -html -out resultados_f.html
```

La búsqueda va a tardar dependiendo de la velocidad de la conexión y la carga de trabajo del NCBI, mientras tanto veamos otras utilidades de **awk**.
---
Mientras tanto vamos a ver otras funcionalidades de **_awk_**:

Si quisieramos separar las secuencias en archivos individuales:

```bash
awk '/^>/{s=++d".fa"} {print > s}' Secuencias_mini.fasta
```

O bien juntarlas:

```bash
awk '1' *.fasta > all .fasta
```

Seleccionar solo las secuencias que tengan un nombre o clave específico:

```bash
awk 'BEGIN {RS=">"} /seq/ {print ">"$0}' Secuencias_mini.fasta
```

Vamos a editar los nombres de las secuencias.

```bash
awk -v dato1="Mis_secuencias" -v dato2="nuevas" '/^>/ {gsub(/^>/,">" $2 "|" dato1 "|" dato2 "-" ); printf "%s\n",$0;next} {print}' Secuencias_mini.fasta
```

### Preguntas
+ ¿Cómo le quitarías esos nuevos nombres a tus secuencias?
+ Genera tu juego de secuencias, cambia el separador y la información de los nombres.

---

Los resultados de la búsqueda ya deben de estar listos, revisa el archivo _resultados_f.html_ con **```grep```**. Copia el archivo a tu computadora y ábrelo con algún navegador.

## Preguntas
* ¿Con qué organismo estamos trabajando?
+ ¿Cuántas secuencias no tienen homólogía en la base de datos? ¿Por qué crees que pasa esto?
* Es posible que en algunos casos obtengamos especies muy divergentes, ¿Por qué crees que pasa esto?
* Hay un factor clave qye puede estar afectando la búsqueda. ¿Cuál es y como podrías solucionarlo?
