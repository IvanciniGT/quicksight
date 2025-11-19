# Relaciones entre variables.. 

## Nominal x Nominal 

Vista el día 1

Sexo x Ubicación -> Tabla de contingencia -> Gráfico de barras apiladas o agrupadas.
                    Indicadores: Phi de Cramer, V Crammer, Coef. de Contingencia (Chi-cuadrado).


## Nominal x Ordinal

Comunidades autónomas (Nominal)
Nivel de satisfacción (Ordinal)

Por definición dijimos que toda variable Ordinal es a su vez Nominal. Por ello, siempre puedo hacer:
- Tabla de contingencia entre las dos variables.
- Gráfico de barras apiladas o agrupadas.
- Indicadores: Phi de Cramer, V Crammer, Coef. de Contingencia (Chi-cuadrado).

> En este caso estaríamos usando solamente la naturaleza nominal de la variable Ordinal (capacidad de clasificar en categorías sin orden).

                                       Niveles de satisfacción
                                 Bajo    Medio   Alto    Muy alto         Total      Mediana
    Comunidades autónomas (Nominal)
    Andalucía                      50      30      15      5               100         Bajo
    Cataluña                       20      40      30      10              100         Medio
    Madrid                         10      20      50      20              100         Alto 
    ...
    Total                          80      90      95      35              300

    Este estudio, lo que ayudaría es a identificar CASILLAS extrañas... que me escaman... Que no encajan con mi valor esperado.

> Pero, si tengo en cuenta la capacidad ORDINAL de la variable de satisfacción, puedo hacer otro análisis.

Lo que querría ver es GROSO MODO, por donde se acumulan los valores de satisfacción para cada comunidad autónoma.
Más o menos, para cada comunidad autónoma, ver "por donde van los tiros" en cuanto a satisfacción.

Podríamos hacer un estudio de comparación de medianas entre comunidades autónomas.
Si las medianas son iguales entre comunidades autónomas, no hay mucha relación entre las variables.
Si las medianas son diferentes, entonces hay relación entre las variables.

## Nominal x Cuantitativa

Teniendo en cuenta que toda variable Cuantitativa es a su vez Nominal (capacidad de clasificar en categorías sin orden), puedo hacer:
- Tabla de contingencia entre las dos variables.
- Gráfico de barras apiladas o agrupadas.
- Indicadores: Phi de Cramer, V Crammer, Coef. de Contingencia (Chi-cuadrado).

Teniendo en cuenta la naturaleza Ordinal de la variable cuantitativa, puedo hacer:
Estudio de comparación de medianas entre categorías de la variable nominal.

Teniendo en cuenta la naturaleza Cuantitativa de la variable cuantitativa, puedo hacer:
Estudio de comparación de medias entre categorías de la variable nominal... Siempre? Dependiendo de lo representativo que sea esa media en cada categoría.

Gráficos adicionales:
- Boxplot múltiple (uno por cada categoría de la variable nominal).
- Histograma múltiple (uno por cada categoría de la variable nominal)
   - Si tengo muchos datos... se ve mal.
   - Cuando tengo una variable nominal dicotómica (2 categorías), puedo hacer una pirámide poblacional.
- Violin plot múltiple (uno por cada categoría de la variable nominal).

## Ordinal x Ordinal

Teniendo en cuenta que toda variable Ordinal es a su vez Nominal, puedo hacer:
- Tabla de contingencia entre las dos variables.
- Gráfico de barras apiladas o agrupadas.
- Indicadores: Phi de Cramer, V Crammer, Coef. de Contingencia (Chi-cuadrado).

Si lo que quiero es identificar casillas extrañas, que no encajan con mis valores esperados.

Teniendo en cuenta la naturaleza Ordinal de una variable, pero la otra la trato como nominal, puedo hacer:
Estudio de comparación de medianas entre categorías de la variable nominal. (de una variable o de la otra... según me interese)

Rango salarial (Ordinal)
Nivel de estudios (Ordinal)

                               Rango salarial
                     Bajo    Medio   Alto    Muy alto         Total
Nivel de estudios
    Sin estudios       40      30      20      10              100
    Primarios          30      40      20      10              100
    Secundarios        20      30      40      10              100
    Universitarios     10      20      30      40              100        
    Total              100     120     110     70              400

Teniendo en cuenta la naturaleza Ordinal de ambas variables, puedo calcular un coeficiente de correlación ordinal (Coeficiente de correlación de Spearman: -1 a 1).
El signo me indica si la relación es directa o inversa (es decir, si a valores altos de una variable, corresponden valores altos de la otra variable o bajos).
El valor absoluto: 0 -> no hay relación; 
                   1 -> relación perfecta (máxima).

## Ordinal x Cuantitativa

De entrada lo mismo que si tengo una Nominal x Cuantitativa
+ Ordinal x Ordinal

## Cuantitativa x Cuantitativa

Gráfico: Scatter plot (diagrama de dispersión... diagrama de puntos)
Indicador: Coeficiente de correlación de Pearson (-1 a 1)... Con una interpretación similar al de Spearman.
           Solamente identifica relaciones lineales entre variables (que sigan una linea recta perfecta).


            Altura
                ^
                |                         X        X      X
                |               X   X     X         X         X     X
                |            X    X
                |            X
                |         C
                |      F
                |   F                 M
                | M
                +-----------------------------------------------------------------> Edad


            Variable1
                ^
                |   
                | X       X             X              X            M 
                |     X           X                X               X
                |    X     X      X       X      M   X      X
                |   X         X      M       X      X   X      X
                |       X   M      X     X     X     X     X     X
                |   X             X    X            X            X
                | M        X      X     X     X     X     X     X     X
                +-----------------------------------------------------------------> Variable2

Un tema con el que hay que tener mucho cuidado a la hora de trabajar con relaciones es...

- Conocimientos de matemáticas y altura de la persona.
  Si mirásemos la relación entre esas variables en personitas de 5 a 20 años... GIGANTESCA! 
  Nosotros sabemos que entre la altura y los conocimientos de matemáticas, no hay realmente una relación de causa-efecto.
  Lo cual no implica, que desde el punto de vista estadístico, no exista una relación entre ambas variables en ese rango de edad.
  Esto es lo que en estadística se llama una relación espuria (aparente), que está condicionada a una variable mediadora: la edad.
  Lo interesante en muchos casos es poder identificar cuanta de esa relación se debe al influjo de esa variable mediadora y cuanta no.
  Y de hecho en el mundo BI, este escenario se da de continuo!

A una compañía de seguros de coche, le importa equivocarse al hacerle seguro a una persona que acaba dándole un parte por tener un accidente?
- Le da igual!
Qué es lo que le importa? Tener clara la probabilidad de cagarla. Es decir, saber de antemano en cuantos casos se va a equivocar.
Si ese dato está claro, la pasta que le cuesta eso la reparte entre todos los clientes, le mete su margen y sigue ganando dinero!

En el mundo BI hay una variable que tenemos siempre. Que guarda relación con todas las demás variables: FECHA / TIEMPO
A nivel de negocio, el tiempo marca todo:
- Las tendencias de compra.
- La estacionalidad.
- Los ciclos económicos.
- Los hábitos de consumo.
- Etc.


---

# Datos de nuestro fichero:            DATAWAREHOUSE
Nombre	                Plataforma	Año	    Genero	    Editorial	        Ventas NA	Ventas EU	Ventas JP	Ventas Otros	Ventas Global
Boulder Dash: Rocks!	DS	        2007	Puzzle	    10TACLE Studios	    0	        0,03	    0	        0	                0,03
Boulder Dash: Rocks!	NES	        2007	Puzzle	    10TACLE Studios	    0	        0,03	    0	        0	                0,03
Panzer Tactics	        DS	        2007	Strategy	10TACLE Studios	    0,06	    0	        0	        0	                0,06

Cuales son las columnas reales.. porque no son esas!
Cuales son los datos que SI TRAE EL FICHERO?

- Nombre: Nombre del videojuego. (Identificador)
- Plataforma: Plataforma en la que se lanzó el videojuego. CONSOLA!         (Cualitativa Nominal)
- Año: Año en el que se lanzó el videojuego.                                (Cuantitativa de intervalo)
- Género: Género del videojuego.                                            (Cualitativa Nominal)
- Editorial: Editorial que lanzó el videojuego.                             (Cualitativa Nominal)
- Ventas: Ventas(en millones de unidades).                                  (Cuantitativa de razón - Frecuencia absoluta)
  Y las frecuencias en los estudios... entran de forma especial: PESOS!
- Zona: Zona geográfica en la que se realizaron las ventas.                 (Cualitativa Nominal)


Si os imagináis el conjunto de datos ORIGINAL, que dió lugar a este fichero: DATALAKE
VENTAS !
Nombre	                Plataforma	Año	    Genero	    Editorial	        Cliente.   Fecha         Donde
Boulder Dash: Rocks!	DS	        2007	Puzzle	    10TACLE Studios	    Menchu     01/02/2008.   Japón
Boulder Dash: Rocks!	DS	        2007	Puzzle	    10TACLE Studios	    Federico   02/02/2008.   Japón
Boulder Dash: Rocks!	DS	        2007	Puzzle	    10TACLE Studios	    Ana        02/02/2008.   Europa
Boulder Dash: Rocks!	DS	        2007	Puzzle	    10TACLE Studios	    Luis       03/02/2008.   NA

---

Tenemos varias opciones en el análisis:
- Analizar Ventas!
- Analizar Juegos!
Ventas por región?

---

Nombre	                Plataforma	Año	    Genero	    Editorial	        Region    Ventas
Boulder Dash: Rocks!	DS	        2007	Puzzle	    10TACLE Studios	    NA	        0,03
Boulder Dash: Rocks!	DS	        2007	Puzzle	    10TACLE Studios	    JP	        0,03
Boulder Dash: Rocks!	NES	        2007	Puzzle	    10TACLE Studios	    0	        0,03

