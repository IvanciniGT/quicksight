
Películas por temática
-----------------------
Desde visualizaciones:
- Recuento distintivo del título de la película .1 ---> FULLSCAN de la tabla visualizaciones + ORDEER BY interno para el DISTINCT
- Recuento distintivo del id de la película     .2.---> FULLSCAN del índice (en este caso no se aplica ORDER BY interno ya que los índices están ordenados por definición)

Desde Películas:
- Recuento del título de la película            .3 ---> FULLSCAN 
- Recuento del id de la película                .4 ---> FULLSCAN del índice


Ordenamos según eficiencia:
        Menos eficiente     ------->     Más eficiente
                1 ----> 3 ----> 2 ---> 4

                4. En el 4 no hay distinct... y además se usa un índice en la BBDD (fruto del foreign key)
                   En los índices, los datos se guardan agrupados. 

Cuidado aquí.
> Si los datos están en Spice... Cuánto me tengo que preocupar yo de esto? De poco a nada

Ya que Spice:
- Genera índices eficientes para mostrar todos esos paneles
- Cachea el resultado de las consultas para que no se tengan que volver a ejecutar

> Si los datos están en BBDD

Cuál es la peor operación (en términos de eficiencia) que podemos hacer en un ordenador? ORDENAR DATOS.
Suena un poco raro... ya que se llaman ordenadores.
Lo que pasa es que en España le llamamos ordenadores a las computadores (que es como se conocen estos cacharros.. y como nuestros hermanos del otro lado del charco se refieren a ellos), no por su capacidad de ordenar datos. Nosotros mal-importamos ese término del francés "ordinateur" (donde se llama así por su capacidad para procesar ORDENES).

De hecho las BBDD hacen todo lo posible para evitar ordenar datos, y entre cosas es el motivo por el que creamos INDICES en las tablas... que no son sino COPIAS ORDENADAS de los datos. Al crear un índice, los datos los duplicamos en otro sitio, pero los ordenamos para que las consultas que necesiten esos datos ordenados no tengan que volver a ordenar los datos originales.

Con qué palabras tengo que tener mucho cuidado en SQL?
- ORDER BY
- DISTINCT (implica ordenar los datos para eliminar duplicados)
- GROUP BY (implica ordenar los datos para agruparlos)
- UNION (que hace un distinct implícito... el union all es genial!)

---

si tengo la tabla peliculas:

| id | título        | id temática  |
|----|---------------|--------------|
| 1  | Toy Story     | 1            |
| 2  | Buscando a... | 1            |
| 3  | Matrix        | 2            |
| 4  | John Wick     | 2            |
| 5  | El Padrino    | 3            |
| 6  | Scarface      | 3            |


Y genero un índice sobre id temática:
| id temática  | ubicación en tabla |
|--------------|--------------------|
| 1            | 1, 2               |
| 2            | 3, 4               |
| 3            | 5, 6               |


En el caso de la tabla visualizaciones, su índice para peliculaId sería:
| peliculaId  | ubicación en tabla visualizaciones |
|-------------|------------------------------------|
| 1           | 1, 2, 3                            |
| 2           | 4, 5                               |
| 3           | 6                                  |





-- Usuarios
-- El campo el email debería validarlo... que lo que metan sea un email
-- Regexp: ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$
CREATE TABLE usuarios (
    id      SERIAL,
    estado  BOOLEAN      NOT NULL   DEFAULT TRUE,
    alta    TIMESTAMP    NOT NULL   DEFAULT CURRENT_TIMESTAMP,
    email   VARCHAR(100) NOT NULL,
    nombre  VARCHAR(100) NOT NULL,

    CONSTRAINT usuarios_email_check CHECK (email ~ '^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$')

) ;

ALTER TABLE usuarios ADD CONSTRAINT usuarios_pk       PRIMARY KEY (id)  ;
ALTER TABLE usuarios ADD CONSTRAINT usuarios_email_uq UNIQUE (email)    ;

-- Directores
CREATE TABLE directores (
    id     SMALLSERIAL,
    nombre VARCHAR(100) NOT NULL

) ;

ALTER TABLE directores ADD CONSTRAINT directores_pk        PRIMARY KEY (id) ;
ALTER TABLE directores ADD CONSTRAINT directores_nombre_uq UNIQUE (nombre)  ;

-- Tematicas
CREATE TABLE tematicas (
    id     SMALLSERIAL,
    nombre VARCHAR(100) NOT NULL --- TODO: PLANTEAR INDICE. Si la app ofrece la lista de temáticas, no es necesario

) ;

ALTER TABLE tematicas ADD CONSTRAINT tematicas_pk        PRIMARY KEY (id) ;
ALTER TABLE tematicas ADD CONSTRAINT tematicas_nombre_uq UNIQUE (nombre)  ;

-- Peliculas
CREATE TABLE peliculas (
    id          SERIAL,
    tematica    SMALLINT     NOT NULL,
    director    SMALLINT     NOT NULL,
    duracion    SMALLINT     NOT NULL,
    fecha       DATE         NOT NULL,
    edad_minima SMALLINT     NOT NULL,
    nombre      VARCHAR(100) NOT NULL, -- TODO: PLANTEAR INDICE
                                       -- Quiero que me puedan hacer búsquedas por nombres.. pero sin importar mayusculas, minusculas, acentos, etc
                                       -- Y que me puedan poner los primeros caracteres de una de las palabras del nombre
                                       -- "LEO" -> "El rey león"
                                       -- Los collate no me resuelven la papeleta: No permiten hacer búsquedas por palabras parciales, ni por palabras
                                       --    Si puedo hacer un LIKE 'palabra%' pero no un LIKE'%palabra%'
                                       -- Los gin con ts_vector no me resuelven la papeleta: No permiten hacer búsquedas por palabras parciales
                                       -- Necesito un índice gin de trigramas
                                       -- Pero los trigramas son sensibles a mayúsculas y minúsculas y acentos
    CONSTRAINT peliculas_fecha_check           CHECK (fecha <= CURRENT_DATE),
    CONSTRAINT peliculas_edad_minima_check     CHECK (edad_minima >= 0),
    CONSTRAINT peliculas_duracion_minima_check CHECK (duracion > 0)

) ;

ALTER TABLE peliculas ADD CONSTRAINT peliculas_pk          PRIMARY KEY (id)         ;
ALTER TABLE peliculas ADD CONSTRAINT peliculas_tematica_fk FOREIGN KEY (tematica)   REFERENCES tematicas (id);
ALTER TABLE peliculas ADD CONSTRAINT peliculas_director_fk FOREIGN KEY (director)   REFERENCES directores (id);

-- Índices
CREATE INDEX peliculas_nombre_idx ON peliculas (tematica) ;
CREATE INDEX peliculas_director_idx ON peliculas (director) ;
-- Índice de trigramas
CREATE EXTENSION IF NOT EXISTS unaccent;

ALTER TABLE peliculas ADD COLUMN nombre_normalizado VARCHAR(100);

-- Crear la función del trigger para actualizar la columna normalizada
CREATE OR REPLACE FUNCTION actualizar_nombre_normalizado()
RETURNS TRIGGER AS $$
BEGIN
    NEW.nombre_normalizado := unaccent(lower(NEW.nombre));
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Crear el trigger
CREATE TRIGGER trg_actualizar_nombre_normalizado
BEFORE INSERT OR UPDATE ON peliculas
FOR EACH ROW
EXECUTE FUNCTION actualizar_nombre_normalizado();




CREATE EXTENSION IF NOT EXISTS pg_trgm;

CREATE INDEX peliculas_nombre_trgm_idx ON peliculas USING gin (nombre_normalizado gin_trgm_ops) ;

-- Indice fecha
CREATE INDEX peliculas_fecha_idx ON peliculas (fecha) ;
-- Visualizaciones

CREATE TABLE visualizaciones (
    usuario  INTEGER    NOT NULL,
    pelicula INTEGER    NOT NULL,
    fecha    TIMESTAMP  NOT NULL  DEFAULT CURRENT_TIMESTAMP

) PARTITION BY RANGE (fecha);
-- Cada fila me ocupa 40 bytes
-- Una fila vacía ocupada 24
-- 4, 4, 8 = 16 + 24 = 40
ALTER TABLE visualizaciones ADD CONSTRAINT visualizaciones_usuario_pelicula_fecha_uq 
    PRIMARY KEY (fecha, usuario, pelicula) ;
-- Order de las columnas en el PRIMARY KEY... No es irrelevante... Al contrario... es bien importante
-- PostgreSQL ya dijimos que en autom. nos genera un INDICE de los PK
-- Ese índice se puede usar para búsquedas...
-- Pero en el índice, la primera ordenación se hace por fecha
-- SOLO se usará ese índice para búquedas en la tabla que filtren por fecha.

-- Dado que va a ser la tabla más grande con diferencia, si se plantease la necesidad de hacer búsquedas en ella
-- por otros campos: USUARIO: Dame las peliculas que ya ha visto este usuario
--  DEBERIAMOS plantear el crear un índice para esa columna

--DROP INDEX visualizaciones_usuario_idx;

    
    
ALTER TABLE visualizaciones ADD CONSTRAINT visualizaciones_usuario_fk                FOREIGN KEY (usuario)  REFERENCES usuarios (id);
ALTER TABLE visualizaciones ADD CONSTRAINT visualizaciones_peliculas_fk              FOREIGN KEY (pelicula) REFERENCES peliculas (id);


CREATE TABLE visualizaciones_2024 PARTITION OF visualizaciones
    FOR VALUES FROM ('2024-01-01') TO ('2025-01-01') ;
-- El año que viene, crear otra tabla
CREATE TABLE visualizaciones_2025 PARTITION OF visualizaciones
    FOR VALUES FROM ('2025-01-01') TO ('2026-01-01') ;
    
CREATE INDEX visualizaciones_usuario_idx ON visualizaciones_2024 USING hash (usuario) ;



SELECT 
    v.fecha,
    p.nombre as titulo,
    p.fecha,
    p.duracion,
    p.edad_minima,
    t.nombre,
    d.nombre as director,
    u.estado,
    u.alta
FROM
(
    (
        (
            (
                peliculas p
                RIGHT JOIN
                tematicas t
                ON p.tematica = t.id
            ) FULL JOIN
                directores d
             ON p.director = d.id
        ) FULL JOIN
            visualizaciones v
         ON p.id = v.pelicula
    ) RIGHT JOIN
      usuarios u
    ON v.usuario = u.id
)




SELECT 
    p.nombre as titulo,
    p.fecha,
    p.duracion,
    p.edad_minima as edad,
    t.nombre,
    d.nombre as director
FROM
        (
            (
                (
                    peliculas p RIGHT JOIN tematicas t ON p.tematica = t.id
                ) FULL JOIN directores d  ON p.director = d.id
            ) 
        )
    
