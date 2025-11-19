
# Quicksight

Es la herramienta de BI de AWS.

---

# BI: Business Intelligence

Aportar información para la toma de decisiones a nivel de negocio. Ayudar a entender los datos.

- Ayudar a entender los datos (representaciones gráficas... resúmenes) <<< ESTADISTICA DESCRIPTIVA

DATO: Tengo una persona que pesa 150Kgs.

Información transporta ese dato?
- Masa de la persona.
- Edad? Esa persona tendrá menos de 16 años? Complejo!
- Talla de ropa? Si! No con total precisión.. pero en bastante medida.
- Sus tendencia alimenticias? Posiblemente.
- Sus hábitos de ejercicio? Posiblemente.

Tener los datos no es lo mismo que entender los datos.

# Qué tipos de datos tenemos? Variables... 

Una cosa son los tipos de datos desde el punto de vista de computación ( Números, textos, fechas, valores lógicos...) y otra cosa son los tipos de datos desde el punto de vista estadístico. Cuál me interesa? AMBOS!

Desde el punto de vista del análisis: Me interesa el punto de vista estadístico.
Desde el punto de vista de almacenamiento y manipulación: Me interesa el punto de vista computacional.

## Tipos de datos según la estadística: Y AQUÍ HAY UN PROBLEMA... las herramientas de BI, salvo gloriosas excepciones, no hablan de tipos de datos estadísticos... hablan de tipos de datos computacionales.

- Cuantitativos: Tienen una Unidad de medida
   - De razón:        Tienen un cero absoluto. Número de hijos de una familia, Salario de una persona, Edad de una persona.
      ¿Qué operaciones matemáticas puedo hacer sobre datos de razón? Sumar, Restar, Multiplicaciones, Divisiones
        Si una familia tiene 2 hijos y otra 4... Tiene sentido decir que una familia tiene 2 hijos menos que otra? SI
                                                 Tiene sentido decir que una familia tiene el doble de hijos que otra? SI
   - De intervalo:    No tienen un cero absoluto. Temperatura en grados Celsius, Año (de una fecha)
      ¿Qué operaciones matemáticas puedo hacer sobre datos de intervalo? Sumar, Restar 
       Si en una ciudad hace 10ºC y en otra 20ºC... Tiene sentido decir que en una ciudad hace 10ºC menos que en otra? SI
                                                    Tiene sentido decir que en una ciudad hace el doble de temperatura que en otra? NO
- Cualitativos:  No tienen unidad de medida
  - Nominales:      Conjunto de NOMBRES, etiquetas para identificar los potenciales valores o modalidades de la variable.
                      Color de ojos, Nacionalidad, Marca de coche 
  - Ordinales:      Conjunto de NOMBRES, etiquetas para identificar los potenciales valores o modalidades de la variable, con un orden implícito entre ellos. 
                        Nivel de estudios (Primaria, Secundaria, Bachillerato, Universidad)
                        Nivel de satisfacción (Muy insatisfecho, Insatisfecho, Neutral, Satisfecho, Muy satisfecho)

Qué puedo hacer con datos nominales? Clasificar, agrupar.
Qué puedo hacer con datos ordinales? Clasificar, agrupar, ordenar.
Qué puedo hacer con datos cuantitativos? Clasificar, agrupar, ordenar, operaciones matemáticas.

Todo dato cuantitativo por definición es ordinal.. Y todo dato ordinal por definición es nominal.

## Tipos de datos según la computación:

- Números (enteros, decimales)
- Textos (cadenas de caracteres)
- Fechas y horas (que realmente son números enteros o decimales con un formato especial)
- Valores lógicos (booleanos: verdadero/falso)

> DNI. Que tipo de dato es?

- Desde el punto de vista computacional: Texto (cadena de caracteres)
- Desde el punto de vista estadístico: Nominal

Un DNI contiene de 2 a 9 caracteres. Todos menos el último son dígitos (0-9). El último carácter debe ser una letra (A-Z).

    23000001 | 23
             +-------
           1  1000000
           ^
           Este es el que me importa: El resto de la división entera entre 23: 0-22
           En nuestro caso, al dividir 23000001 entre 23, el resto es 1 y le corresponde la letra R.

Cómo guardo aquello en una BBDD?

> Texto VARCHAR(9). Cuanto ocuparía esto?

Cuánto ocupa un carácter en BBDD? Depende del juego de caracteres (codificación) que use la BBDD.
- ASCII: 1 byte por carácter
- ISO-8859-1: 1 byte por carácter
- UTF-8: De 1-4 bytes por carácter (depende del carácter)
- UTF-16: 2 o 4 bytes por carácter (depende del carácter)
- UTF-32: 4 bytes por carácter

Dado que los caracteres que se usan en un DNI son de los más comunes, letras simplonas y números, y que quicksight usa UTF-8, podemos asumir que cada carácter ocupa 1 byte.
Entonces, un DNI ocuparía 9 bytes.

> NUMERO por un lado como número entero y LETRA por otro como carácter.

Para el número con 4 bytes (<= 4.294.967.295) me vale.
Para la letra con 1 byte (<= 256 caracteres) me vale.

Por tanto de esta forma, el dni ocuparía 5 bytes.

> GUARDO SOLO EL NUMERO. Y LA LETRA LA CALCULO CUANDO ME HACE FALTA.

En este caso, me vale con 4 bytes... aunque tendría más consumo de recursos al tener que calcular la letra cada vez que la necesito.

DECISIONES!

Con la segunda opción,(5bytes) ahorro 4 bytes por cada dni que guarde (si lo comparo con la primera opción). Esto es un ahorro del 44% de espacio.
Básicamente la mitad.
---

# El almacenamiento a día de hoy es caro o barato?

El almacenamiento lo más caro que hay en un entorno de producción... Con mucha diferencia.

Estamos acostumbrados a ir al MediaMarkt a por un disco de 2 Tbs por 50€.
Ese disco es un disco de baja calidad, pensado para un uso doméstico.
En casa, usaré el disco 2 veces al mes.
En la empresa el disco se usa 24x7. 

Oracle, un nvme de 2tbs lo vende por 3500€

3500/50 = 70 veces más caro!

Pero esto es la punta del iceberg.
En un entorno de producción, cada dato se guarda al menos por triplicado.... x3 precio ... sobre un precio que ya ees un x20-70 <- ALTA DISPONIBILIDAD
Y ahora mete backups <- RECUPERACION ANTE DESASTRES. Normalmente guardo copias dee los datos de al menos 2 semanas atrás. <- x2-4 precio

Esos 2 Tbs que en casa me cuestan 50€, en la empresa me cuestan 50*70*3*3 = 31.500€

Si los datos que tengo me ocupan el doble, necesito invertir el doble de tiempo para leerlos, escribirlos, mandarlos por red, procesarlos en la cpu.

---
### Cómo guardan al final las computadoras los datos? Qué lenguaje hablan por dentro las computadoras?

BINARIO (0y1s) Las computadoras solo entienden de estados binarios.

La mínima unidad de información que puedo guardar sería lo que llamamos un bit:
[ ]
 0
 1

En un bit puedo guardar 1 dato... que potencialmente puede tener un valor a elección entre 2 posibilidades (0 o 1).
Eso me vale por ejemplo para el sexo de una persona (Hombre/Mujer).
Me vale pasa saber si una persona tiene dinero o no (Sí/No).

Pero me sirve para guardar el color del pelo de una persona? Rubio, Castaño, Moreno, Pelirrojo, Canoso... Necesito más potenciales valores
Entonces, empiezo a juntar bits:
8 bits = 1 byte
2 bits cuántos valores pueden representar? 2^2 = 4 valores
    00
    01
    10
    11
Qué significan esos valores? Lo que yo quiero. Yo humano, le pongo significado a esos valores.
3 bits cuántos valores pueden representar? 2^3 = 8 valores
    000 Rubia
    001 Castaña 
    010 Morena
    011 Pelirroja
    100 Canosa
    101 Otros
    110 No lo uso
    111 No lo uso

En un byte, cuantos valores puedo representar? 2^8 = 256 valores 

                    ENTERO POSITIVO     ENTERO CON SIGNO    CARÁCTER
    0000 0000           0                    -128               a
    0000 0001           1                    -127               b
    0000 0010           2                    -126               c
    ...
    1111 1110         254                     126   
    1111 1111         255                     127  

Qué significan esos valores? Lo que yo quiero. Yo humano, le pongo significado a esos valores: TIPOS DE DATOS COMPUTACIONALES

Cuántos caracteres usa la humanidad? > 150.000 Hay un estándar que los recoge: UNICODE
Me entran 150.000 posibles valores en 256? (1 byte) NO
2 bytes? 2^16 = 65.536 valores. NO
4 bytes? 2^32 = 4.294.967.296 valores. SI

Existen diferentes juegos de caracteres (codificaciones) que usan diferentes números de bytes para representar cada caracter.

Los americanos, toman sus letras (mayúsculas y minúsculas), los números, signos de puntuación y algunos símbolos especiales y les dan menos de 256 valores (1 byte). ASCII
Los españoles(o gente de habla hispana), tomamos las letras mayúsculas y minúsculas, los números, signos de puntuación y además las letras con acento y la ñ, la ç.. y alghunos menos caracteres especiales y les damos menos de 256 valores (1 byte). ISO-8859-1.

Unicode que recoge todos los caracteres del mundo, necesita más de 256 valores.
Hay varias formas de representar esos 150.000 caracteres:
- UTF-8: Usa entre 1 y 4 bytes por carácter. Los caracteres más usados en occidente usan 1 byte. Los caracteres menos usados usan más bytes.
- UTF-16: Usa entre 2 y 4 bytes por carácter. Los 65.536 primeros caracteres más usados usan 2 bytes. El resto usan 4 bytes.
- UTF-32: Usa siempre 4 bytes por carácter.

Hoy en día un estándar es UTF-8 (y lo que usa Quicksight)


> En los programas de BI

Solo queremos NUMEROS ! No quiero ni un texto (a veces no me queda más remedio que dejar alguno)... y las fechas? Esas no me preocupan .. son números.
Necesitamos tener los textos codificados como números para poder hacer cálculos con ellos.

---

# Vocabulario habitual en el mundo de los datos

Base de datos.                  Las tenemos en los entornos de producción.
   vvv
   ETL: Scripts de extracción, transformación y carga.
            ETL: extraemos de un origen, transformamos los datos y los cargamos en otro sitio.
            ELT: extraemos de un origen, lo cargamos en otro sitio y luego transformamos los datos.
            TEL: transformo los datos en el origen y luego los extraigo y los cargo en otro sitio.
            TELT: transformo los datos en el origen, los extraigo y los cargo en otro sitio.
            TETL: transformo los datos en el origen, los extraigo, los transformo de nuevo y los cargo en otro sitio.
            (en ocasiones estos scripts los ejecutamos en tiempo real, en otras ocasiones los ejecutamos a ciertas horas planificadas)
   vvv
DataLake                        Aquí guardamos los datos en bruto, tal cual vienen de las bases de datos de producción (más o menos)
   vvv      
   ETL
   vvv
DataWarehouse                   Otro almacén de datos. Aquí guardamos los datos ya transformados, limpios y organizados para un determinado tipo de análisis.
                                    Modelos de datos en estrella, copo de nieve...
   vvv
Business Intelligence           Aplicar técnicas estadísticas básicas (estadística descriptiva nivel instituto) para entender los datos y extraer información 
                                relevante para la toma de decisiones. Vamos a mezclar la información de distintos datos(variables) para obtener conclusiones.
                                Básicamente aquí podemos llegar a juntar como mucho 2/3 variables.
   vvv
Data mining                     Técnicas estadísticas/computación más avanzadas (regresiones, clustering, series temporales...) para entender los datos y extraer información 
                                relevante para la toma de decisiones. Le pedimos a la computadora que haga ese estudio por nosotros.
   vvv
Machine Learning                Tratamos de predecir comportamientos futuros basándonos en datos históricos. Aquí ya estamos hablando de modelos predictivos.

---

# Bigdata

Manejo de grandes volúmenes de datos.
Datos muy complejos
Datos que se generan a gran velocidad... o al menos a una velocidad demasiado alta para la ventana de vide útil que tenemos.

Tiene que ver más con la forma en la que nos enfrentamos a un problema a la hora de lidiar con datos, que cumplen alguna de las características anteriores.
En este caso, usamos un enfoque híbrido, que implica tanto hardware como software específico para lidiar con esos datos.
- En ocasiones a lo mejor solo quiero almacenar esos datos.
- Otra veces quiero analizarlos.
- Hay momentos donde solo quiero transmitirlos a otro sitio.

Puedo hacer estas cosas con las técnicas que he venido usando en las últimas décadas?

> Quiero hacer la lista de la compra: Producto x Cantidad

- Si tengo 50 cosas: Block de notas
- Si tengo algunas más: Excel
- Si tengo más de 100k? El Excel se empieza a hacer caquita! MySQL/MariaDB/PostgreSQL
- Si tengo 100 millones de cosas? MySQL se hace caquita! MS SQL Server
- Si tengo más de 1kM de cosas? MS SQL Server se hace caquita! Oracle DB
  Y cuando el Oracle, en un exadata ya no da más de sí? 

> Juegos de teléfonos. 2v2

En un segundo, un jugador hace 2 movimientos => 6 mensajes que hay que despachar.
Somos 4 jugando: 24 mensajes por segundo.
50k partidas simultáneas: 1.2M mensajes por segundo.
Qué servidor y qué programa es capaz de asegurar la entrega de 1.2 millones de mensajes por segundo? 
OLVIDATE!

> Tengo un pincho USB de 16Gbs... limpio de polvo y paja.

Y tengo un video de 5Gbs... puedo guardarlo en el pincho? Depende del formato que haya aplicado al disco.
- FAT 16: 2 Gbs máximo por archivo.
- NTFS, exFat, ZFS... Y cuando llego al límite de esos.
- Archivo de 5Pbs.. en qué HDD guardo eso?

Enfoque BIGDATA es... vamos a usar en lugar de una megacomputadora, un montón de maquinitas de mierda (commodity hardware) y vamos a repartir el problema entre todas ellas.
Para qué uso esto?
- Almacenamiento masivo de datos.
- Analítica masiva de datos.
- Transmisión masiva de datos.

---

# Análisis de datos en BI

Vamos a estar aplicando técnicas de estadística descriptiva básicas.

Tener los datos no es lo mismo que entender los datos.
Para entender los datos es para lo que vamos a usar esas técnicas estadísticas.

Lo primero que queremos es RESUMIR LOS DATOS.

Lo que queremos lo primero es contar cuantos datos tenemos con cada valor de una variable: FRECUENCIA.
Lo primero será crear tablas de frecuencia.

       Nivel de estudios        Frecuencia (abs)  Frq %
       -----------------------------------------------
       Bajo                         10            16 %
       Medio                        20            32 %
       Alto                         25            40 %
       Muy alto                      5             8 %

    Esta tabla de frecuencias posteriormente puedo representarla gráficamente:

      Barras : Frecuencia absoluta
      Sector : Frecuencia relativa (porcentajes)
    
    En ocasiones (solo en ocasiones) tiene sentido calcular: Frecuencias acumuladas (absolutas y relativas)

                                                            Frecuencias acumuladas:
       Nivel de estudios        Frecuencia (abs)  Frq %     absoluto         relativo
       ---------------------------------------------------------------------------------
       Bajo                         10            16 %        10              16 %
       Medio                        20            32 %        30              48 %
       Alto                         25            40 %        55              88 %
       Muy alto                      5             8 %        60             100 %
    
    Eso que he calculado.. que he podido calcularlo.. tiene sentido? Tiene interpretación?
        El 88% de las personas tienen un nivel de estudios ALTO o inferior.
        Solo el 12% de las personas tienen un nivel de estudios MUY ALTO.
    Eso solo tiene sentido si los datos están ordenados de alguna forma lógica.
     - En nuestro caso, los datos están ordenados en la tabla de frecuencias en base al nivel de estudios (ya que la variable es ordinal)
     - Para una variable nominal, tendría sentido calcular frecuencias acumuladas? Puede tenerlo, si buscamos una forma de ordenar los datos de alguna forma lógica (por ejemplo en base a la frecuencia)


                                                            Frecuencias acumuladas:
       Color de pelo.          Frecuencia (abs)  Frq %     absoluto         relativo
       ---------------------------------------------------------------------------------
       Rubio                          10            16 %        10              16 %
       Castaño                        20            32 %        30              48 %
       Moreno                         25            40 %        55              88 %
       Muy Pelirrojo                   5             8 %        60             100 %
    

                                                            Frecuencias acumuladas:
       Color de pelo.          Frecuencia (abs)  Frq %     absoluto         relativo
       ---------------------------------------------------------------------------------
       Moreno                         25            40 %        25             40 %
       Castaño                        20            32 %        45             72 %
       Rubio                          10            16 %        55             88 %
       Pelirrojo                       5             8 %        60            100 %
    
        > Entre los 2 colores de pelo más comunes, tenemos el 72% de las personas.

        > Entre mis 3 principales clientes me suponen el 80% de mis ventas.


    Frecuencia
        ^
        |
     25 |    X
        |    X               X
        |    X               X
        |    X               X              X
        |    X               X              X              X
        +--------------------------------------------------------------------- Variable
            Moreno         Castaño        Rubio       Pelirrojo

    Frecuencia
        ^
        |
     25 |                                                  X
        |                                   X              X
        |                                   X              X
        |                    X              X.             X
        |    X               X              X              X
        +--------------------------------------------------------------------- Variable
            Pelirrojo       Rubio        Castaño      Moreno        



---

# Qué pasa con las variables cuantitativas? Lee podemos hacer una tabla de frecuencias? SALARIO!

Las variable cuantitativas, a priori van a tener un montón de posibles valores.... lo que daría lugar a una tabla enorme... En ese caso, no estaríamos resumiendo nada.
Lo que podríamos hacer es: Generar una nueva variable ORDINAL, a partir de la variable cuantitativa (RANGO)


         Rango salarial        Frecuencia (abs)  Frq %
         -----------------------------------------------
         0-1000                 10            16 %
         1001-2000              20            32 %
         2001-3000              25            40 %
         3001-4000               5             8 %

    Cómo representamos esto gráficamente? La tabla, igual que antes.
      Barras : Frecuencia absoluta
      Sector : Frecuencia relativa (porcentajes)
    
    Los datos de la variable cuantitativa, habría otra forma de representarlos? HISTOGRAMA



    Frecuencia

        ^
        |
     25 |    X
        |    X               X
        |    X               X
        |    X               X              X
        |    X               X              X              X
        +--------------------------------------------------------------------> Salario
            0-1000       1001-2000      2001-3000     3001-4000


        ^
        |
     25 |    X
        |    X               X
        |    X               X
        |    X               X              
        |    X               X                             X
        +--------------------------------------------------------------------> Salario
            0-1000       1001-2000      2001-3000     3001-4000



    Poligonal de frecuencias

        ^
        |
     25 |    XXXXXXXX
        |XXXX        XXXXXXXXXX
        |                      XXXXXXXXXXXXXXXXXXX
        |                                         XXXXXXXXXXXXXXX
        |                                                         XXXX
        +--------------------------------------------------------------------> Salario
            0-1000       1001-2000      2001-3000     3001-4000

    El área que queda encerrada bajo la poligonal de frecuencias... qué representa? El número total de datos

---

Esta es la forma más básica de resumir los datos que nos podemos encontrar en BI.
A partir de aquí qué? Quiero seguir resumiendo datos!

Tenemos los estadísticos básicos:
- Indicadores de tendencia central:  Groso modo, "por donde van los tiros"
  - Moda        El valor que más se repite, el que "está de moda". Puede haber más dee una moda.
                Qué es lo único que hace falta para calcular una moda? Agrupar datos y contarlos = Tener la tabla de frecuencias
                A qué tipo de variable le puedo calcular la moda? A cualquier tipo de variable (nominal, ordinal, cuantitativa)
  - Mediana     El valor que divide en 2 partes iguales (o lo intenta) al conjunto de datos ordenados.
                Qué es lo único que hace falta para calcular una mediana? Ordenar los datos.
                A qué tipo de variable le puedo calcular la mediana? A variables ordinales y cuantitativas.
  - Media       Responde a una definición matemática concreta: Sumar todos los valores y dividir entre el número de datos.
                La forma de interpretarlo es: La contribución de cada sujeto al total si todos contribuyeran por igual.
                A qué tipo de variable le puedo calcular la media? Solo a variables cuantitativas.

    Nivel de satisfacción de un cliente (0-10)... puedo calcularle la media? NI DE COÑA ! Esto es un error conceptual!
        CUALITATIVA.  Esta variable NO TIENE una unidad de medida (nivel de satisfacción) que sirva de patrón.
            Una satisfacción de 5 mía es igual a una satisfacción de 5 tuya? NO! Es subjetivo.
            Esto sería lo mismo que haber dicho: Nivel de satisfacción: 
                Extraordinariamente bajo     0
                Muy bajo                     1
                Bajo                         2
                Medio                        3
                Alto                         4
                Muy alto                     5
                Extraordinariamente alto     6
    Número de hijos de  una familia     (0-10)... puedo calcularle la media? SI PUEDO!
        CUANTITATIVA. Esta variable TIENE una unidad de medida (número de hijos)

    Si tengo una variable nominal: Voy a usar la moda... no hay más remedio.
    Si tengo una variable ordinal: Puedo usar la moda o la mediana.
    Si tengo una variable cuantitativa: La moda casi no la usamos.
                                        Tendemos a usar mucho (abusar) de la media... Y cuidado.
                                        La media puede ser una mentirosa de narices!!!!
                                        No siempre tiene sentido usar la media.


> Ejemplo

>> Villa arriba de arriba: 10 vecinos.. con sus coches. Y cada uno contamina al día:
        
        5 5 5 10 10 10 10 15 15 15 

        Cuál es la media?           10 gCO/día
        Cuál sería la mediana?      10 gCO/día

            ^
            |            X
            |   X        X         X
            |   X        X         X                   Distribución de datos SIMETRICA !
            |.  X        X         X
            +------------------------------ Contaminación (gCO/día)
                5       10        15
                         ^
                      Mediana
                         ^ 
                       Media


>> Villa arriba de abajo: 10 vecinos.. con sus coches. Y están muy sensibilizados con el tema de la contaminación. Cada uno contamina al día:

        5 5 5 5 5 5 5 5 5 1000

        Cuál es la media?           104,5 gCO/día
        Cuál sería la mediana?      5 gCO/día

            ^
            |  x
            |  X
            |  X                                    Distribución de datos ASIMETRICA !
            |  X
            |  X                            x
            +-------------------------------------------- Contaminación (gCO/día)
               5        104,5             1000
               ^
            Mediana
                         ^ 
                       Media >>>>>
                                                 ^
                                                 |  x
                                                 |  X
                                                 |  X                                    Distribución de datos SIMETRICA !
                                                 |  X
            x                                    |  X                            x
<------------------------------------------------+-------------------------------------------- Contaminación (gCO/día)
           -955                                     5        104,5             1000
                                                    ^
                                                  Mediana
                                                    ^ 
                                             <<<<< Media >>>>>



    La media, es lo que aporta cada vecino al total de contaminación si todos aportaran lo mismo. Pero eso tiene sentido en villa arriba de abajo? NO!
    Atendiendo a la media, podríamos llegar a la conclusión errónea de que los vecinos de villa arriba de abajo contaminan más que los de villa arriba de arriba. O que están menos sensibilizados con el tema de la contaminación. CRASO ERROR!

    El objetivo de todo esto es RESUMIR LOS DATOS para entenderlos mejor.
    La media nos ayuda a entender mejor los datos en este caso? Es representativa del colectivo? NO
    Y si pongo esa media, en un informe, quién interpreta eso, posiblemente saque conclusiones erróneas. Los humanos, al veer una media tendemos a pensar que es representativa del colectivo... y que todos los sujetos del colectivo andan más o menos por ahí.
    En villa arriba de abajo, la mediana sería una mejor representante del colectivo.

Por qué? Que es lo que jode? 
- Los valores extremos o atípicos... NO REALMENTE
- Lo que jode es la forma (simetría o asimetría) de la distribución de los datos.

La mediana es lo que en estadística denominamos un indicador robusto (ya que no se ve afectado por la forma de la distribución de los datos)... en cambio es un indicador NO SUFICIENTE (ya que no nos utiliza la información de todos los datos para su cálculo).
La media es un indicador NO ROBUSTO (ya que se ve afectado por la forma de la distribución de los datos)... en cambio es un indicador SUFICIENTE (ya que nos utiliza la información de todos los datos para su cálculo).

En la práctica lo que hacemos es calcular ambos indicadores (media y mediana) y ver si hay "mucha" diferencia entre ellos (el concepto de mucha, depende de mi criterio subjetivo).
Si estoy midiendo salarios ... y me da una diferencia de 10€ entre media y mediana... son iguales... Los considero iguales.
Si estoy midiendo el precio de una camiseta... 10€ de diferencia entre media y mediana... ya no son iguales.

A ser posible trabajo con media!... si es que es representativa.. pero por otro motivo adicional:

NUNCA JAMAS! puedo dar en un informe un indicador de tendencia central sin su medida de dispersión asociada.

> Portales de vecinos. Cada vecino mide de alto una determinada cantidad.

>> Portal 1: 
   
    170 170 170 170
        Media:          170cm
        Mediana:        170cm

>> Portal 2:
  
    160 170 170 180
        Media:          170cm
        Mediana:        170cm

>> Portal 3:

    160 160 180 180
        Media:          170cm
        Mediana:        170cm

>> Portal 4

    155 165 175 185
        Media:          170cm
        Mediana:        170cm

Resulta que tengo 4 portales (colectivos) totalmente diferentes entre sí... y todos tienen la misma media y mediana.
Si solo pongo media/mediana en el informe, que va a pensar la gente al leerlo? Que son iguales... nada más lejos de la realidad.

Siempre debo acompañar la medida de tendencia central con una medida de dispersión.

- Indicadores de dispersión... Me dice groso modo, cuánto cambian las cosas con respecto al "por donde van los tiros"


>> Portal 1: 
   
    170 170 170 170
      0   0   0   0     < Cuánto cambia cada uno con respecto a la media? Puntuación diferencial
      0.  0.  0.  0.     Cuadrado de las puntuaciones diferenciales
        Media:          170cm
        Media de los valores absolutos de la puntuación diferencial: 0cm <--- Desviación media (NO LA USAMOS YA !)
        Media de los cuadrados de la puntuación diferencial: 0cm^2       <--- Varianza 
        Le hacemos la raíz cuadrada de la varianza para quitar el cuadrado de la unidad: DESVIACIÓN TÍPICA o DESVIACIÓN ESTÁNDAR
        Desviación típica: 0cm

>> Portal 2:
  
    160 170 170 180
     10   0   0  10
     100  0  0 100
        Media:          170cm
        Media de los valores absolutos de la puntuación diferencial: 5cm 
        Media de los cuadrados de la puntuación diferencial: 50cm^2
        Desviación típica: 7,07cm

>> Portal 3:

    160 160 180 180
     10  10  10  10
     100 100 100 100
        Media:          170cm
        Media de los valores absolutos de la puntuación diferencial: 10cm
        Media de los cuadrados de la puntuación diferencial: 100cm^2
        Desviación típica: 10cm

>> Portal 4

    155 165 175 185
     15  5   5   15
     225 25  25 225
        Media:          170cm
        Media de los valores absolutos de la puntuación diferencial: 10cm
        Media de los cuadrados de la puntuación diferencial: 125cm^2
        Desviación típica: 11,18cm

Cómo interpreto la desviación típica? 

    Si tengo un salario en la empresa de 2000€ y la desviación típica es de 300€... 
      La mayoría de la gente (al menos el 50%) tendrá un salario entre 1700€ y 2300€ (2000±300)
      No solo la mayoría.. sino la mayoría más representativa (los que están más cerca de la media)

      Y esto se cumple siempre. Nos lo dice la regla de Chebyshev.

Problema de la desviación típica... Se calcula en base a la media.. Y si la media no ees un buen representante del colectivo? La desviación típica tampoco lo será.

Cada media de tendencia central tiene su propia medida de dispersión asociada.

MEDIA -> DESVIACIÓN TÍPICA
MEDIANA -> RANGO SEMI-INTERCUARTIL

---

# Medidas de posición

- Percentiles: 99
- Cuartiles: Q1 (25%), Q2(50%), Q3(75%)
- Deciles: 9
- Mediana: 1 (50%)

Nos informan del % de los sujetos que tienen un valor inferior a un determinado valor de referencia:

    P95 de los salarios es 4600€: El 95% de los trabajadores tienen un salario inferior a 4600€

La mediana = Q2 = P50: El 50% de los trabajadores tienen un salario inferior a X€

Hay una representación gráfica que hacemos adicionalmente de los datos cuantitativos (podría hacerse de ordinales... pero es mucho menos habitual)

> Diagramas de caja y bigote : Boxplot

                RIC = Q3-Q1             1.5 veces RIC
             <----------------------><-----------------------------------><----------------------------------->

    |        +--------+-------------+                                    |
    |        |        |             |                                    |
    +--------+        |             +------------------------------------+         X         X                              o
    |        |        |             |                                    |
    |        +--------+-------------+                                    |

             Q1(1500) Q2(2000)     Q3(3000)
             V        V            V
  ------------------------------------------------------------------------------------------------------------------------> Salario(Valor)
   "Min razonable"                                               "Máximo razonable"

   1200                                                                12000


Cuánta gente tengo dentro de la caja? Q3 - Q1 = 50% de la gente
 Q3-Q1 = RANGO INTERCUARTIL
 Si lo divido a la mitad = (Q3-Q1)/2 = RANGO SEMI-INTERCUARTIL

 Cómo see interpreta ese rango semi-intercuartil?
  Lo interpretamos de forma análoga a la desviación típica.
  Si tengo un salario mediano de 2000€ y un rango semi-intercuartíl de 500€
    La mayoría de la gente (al menos el 50%) tendrá un salario entre 1500€ y 2500€ (2000±500)
    
    
La forma de interpretarlo es la misma que la desviación típica.


De forma habitual, consideramos valorees atípicos (outliers) aquellos que están a más de 1.5 veces el rango intercuartil por encima de Q3 o por debajo de Q1.
Si están a más de 3 veces el rango intercuartil, los consideramos valores extremos.

Hay algo parecido con la media y la desviación típica?
Sí. Valores atípicos (outliers) aquellos que están a más de 2 desviaciones típicas por encima de la media o por debajo de la media.
Si están a más de 3 desviaciones típicas, los consideramos valores extremos.


Todo lo que hemos estado haciendo hasta ahora son estudios / resúmenes de cada una de las variables por separado.
Cogemos cada variable y la sometemos a este estudio. Eso me ayuda a entender esa variable .. o mejor dicho, como se comporta mi colectivo en relación a esa variable.

La gracia de todo esto es cuando quiero entender cómo se comporta mi colectivo en función de 2, 3 o 4 variables a la vez.

Si lo pensamos... el mundo BI trata de aportar datos para la toma de decisiones.
Esa toma de decisiones afectará al FUTURO!
Los datos son pasados!
De alguna forma, lo que estamos haciendo es intentar adivinar el futuro, basándonos en datos del pasado.

Es decir.. todo esto, no va a ser sino una forma BURDA de MACHINE LEARNING.
Es machine learning.. a ojo de buen cubero.

Hay muchas formas de adivinar el futuro:
- Posos del café
- Bolita de cristal
- Tirada de cartas
- Lectura de manos
- Analizar datos históricos y buscar patrones que me ayuden a predecir el futuro. <<<< ESTO SIGUE SIENDO UNA PREDICCIÓN!
  Esto se basa en una suposición muy fuerte: El mundo va a seguir funcionando igual que hasta ahora.

Una forma un poco más evolucionada de esto es la estadística INFERENCIAL! Een general no nos metemos mucho con eso en BI.
La estadística inferencial lo que ofrece es la probabilidad de cagarla al tomar una decisión.... aunque sigue suponiendo que el futuro va a ser igual que el pasado.

Para hacer esas predicciones, siempre vamos a jugar al menos con 2 variables.
1 conocida... otra desconocida... esa es la que quiero predecir.
Sé que viene el mes de DICIEMBRE (Variable conocida)
Quiero predecir las ventas de bufandas de DICIEMBRE (Variable desconocida)

Y de alguna forma pensamos que hay relación entre esas variables. Y apoyándonos en esa relación, hacemos la predicción.
Entender el comportamiento de una variable aislada es el principio... pero no es suficiente.

En BI, necesitamos entender cómo se comportan 2 o más variables a la vez. Qué relaciones hay entre ellas... y aquí es donde se pone complicado.
2 variables, nuestro cerebro las puede manejar bien.
3 variables, ya se complica bastante.
Imposible manejar más de 4 variables a la vez. <<<< Y esto es lo que dejamos para el Data Mining.

---

# Relaciones entre variables.. 

## Nominal x Nominal

Sexo         Nominal
Ubicación    Nominal

Tabla de contingencia (Una tabla de frecuencias donde tanto en filas como en columnas tengo los valores de las variables)

                                Sexo (50% Hombre, 50% Mujer)
                          ------------------------------------
          Ubicaciones       Hombre           Mujer      Total
          - Bus                25              25        50
                               16              34                Me cuadra? o me escama? Es bastante normal.. No nos llama la atención  
          - Tren               50              50       100
                               55              45                Me cuadra? o me escama? Es bastante normal.. No nos llama la atención 
          - Mercado           100             100       200
                              110              90                Me cuadra? o me escama? Es bastante normal.. No nos llama la atención 
          - Médico             25              25        50
                                1              49                Me cuadra? o me escama? Aquí ya me escama un poco... Por qué hay tanta diferencia?
                                ** AQUÍ PASA ALGO RARO! (1) 
          - ...
  Lo que hemos puesto son los valores ESPERADOS... Lo que esperaría encontrar 
  Ahora voy y subo al bus.. y cuento la REALIDAD

    (1) existe una relación entre Estar en el médico y el Sexo... ya que si no hubiera relación, me habría encontrado más o menos a la cantidad de gente que yo esperaba.
        La estadística SOLO ME AYUDA A PONER EL FOCO en ello. No me da respuestas.

    Dónde está el problema:
    1. Las relaciones no son puras... no se dan o no se dan. Se dan en mayor o menor medida.
            Este año, está de moda el color rojo. Eso significa que hay relación entre color favorito y año
            El año pasado estuvo de moda el color azul. 

            Ahora.. que este año esté de moda el color rojo significa que hay más gente vistiendo de rojo que esperaría encontrar.
                    que el año pasado estuviera de moda el color azul significa que había más gente vistiendo de azul que esperaría encontrar.

                    Pero esto significa que el año pasado pegase el azul con la misma intensidad que este año el rojo? NO!
                    Puede ser que este año haya pegado más el rojo, pero que lo haya hecho con menor intensidad que el azul el año pasado.

                    La pregunta no va a ser si me escama o no ... sino CUANTO ME ESCAMA?
    2. Cómo unifico criterios... y dónde pongo el corte.


    En estadística descriptiva usamos indicadores de asociación para determinar cómo de fuerte es esa relación entre variables.
    Cuando tenemos 2 variables nominales, de esa tabla podemos calcular un indicador llamado chi-cuadrado.
    Lo que pasa es que no aporta mucho... Ya que su valor está entre 0 y infinito.

    Lo que hacemos es de ese chi-cuadrado calcular otros indicadores derivados que nos dan valores entre 0 y 1.
    - Cramer V
    - Phi
    - Contingency Coefficient
    En general son muy similares entre si.
        0 = No hay relación entre variables
        1 = Relación perfecta entre variables = RUINA desde el punto de vista de un estudio! 
            Lo que pasa es que estaría midiendo la misma cosa... de 2 formas diferentes.

    Esa tabla, que hemos creado, se puede representar gráficamente?
        - Diagrama de barras apiladas o agrupadas.
        - Esa tabla también la podemos representar como un mapa térmico (heatmap)
