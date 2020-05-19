## Índice
 - [DDL Definición de la estructura](#ddl---definici%c3%b3n-de-la-estructura)
   - [Instrucción CREATE](#instrucci%c3%b3n-create)
     - [CREATE SCHEMA](#create-schema)
     - [CREATE TABLE](#create-table) 
       - [Tipos de datos - Dominio](#tipos-de-datos---dominio)
         -  [Crear dominios](#crear-dominios)
       - [Restricciones de atributos (Constraints)](#restricciones-de-atributos-constraints)
   - [Instrucción DROP](#instrucci%c3%b3n-drop)
   - [Instrucción ALTER TABLE](#instrucci%c3%b3n-alter-table)
 - [DML Manipulación de datos](#dml---manipulaci%c3%b3n-de-datos)
   - [Instrucción INSERT](#instrucci%c3%b3n-insert)
   - [Instrucción UPDATE](#instrucci%c3%b3n-update)
   - [Instrucción DELETE](#instrucci%c3%b3n-delete)

# DDL - Definición de la estructura
**DDL** (Data Definition Language) es el sub-lenguaje de SQL que agrupa los comandos necesarios para crear, modificar y borrar las estructuras de SQL como pueden ser los esquemas o las tablas

>**¡OJO!** Esta sección está documentada para el gestor de bases de datos **PostgreSQL**, las instrucciones y su sintaxis pueden ser diferentes en otro gestor de bases de datos

## Instrucción CREATE
Esta es la instrucción que usaremos para crear elementos en nuestra base de datos. Podemos crear dos elementos principalmente, un schema y una tabla:
### CREATE SCHEMA
Un schema dentro de nuestra base de  datos es una colección de elementos (tablas principalmente). La sintaxis es la siguiente:
```sql
CREATE SCHEMA [IF NOT EXISTS] <nombre_schema>;
```
> "IF NOT EXISTS" hace que no se cree el schema si ya existe uno con el mismo nombre. Que aparezca entre corchetes significa que es opcional ponerlo

### CREATE TABLE
Las tablas en las bases de datos son los elementos que guardan los atributos (y sus valores) de una determinada entidad. En el momento de crear la tabla se definen los atributos que va a tener y a qué tipo de datos pertenecen (dominio). Opcionalmente se pueden incluir restricciones, que se explican más adelante. A la hora de definir una tabla hay que asignarle una **clave primaria**, que no se puede repetir y que no puede ser nula. Esta clave se utilizará para identificar cada fila de la tabla. La sintaxis es la siguiente:
```sql
CREATE TABLE [IF NOT EXISTS] <nombre_de_la_tabla>(
  <nombre_atributo_1> <dominio_atributo_1> <restricción_1> <restricción_2> ...,
  <nombre_atributo_2> <dominio_atributo_2> <restricción_1> <restricción_2> ...,
  ... ,
  PRIMARY KEY (nombre_atributo_clave_primaria)
);
```
> Si la clave primaria está compuesta por más de un atributo, se pone dentro de los paréntesis de PRIMARY KEY cada uno de los atributos, separados por comas.
```sql
CREATE TABLE IF NOT EXISTS alumnos(
  dni CHAR(9),
  nombre VARCHAR(30) NOT NULL,
  edad INTEGER,
  fNacimiento DATE,
  PRIMARY KEY (dni)
);
```
> Ejemplo de creación de una tabla donde "dni" es la clave primaria

> Se puede hacer que un atributo no pueda tomar valores nulos, poniendo "NOT NULL" después del dominio
```sql
CREATE TABLE IF NOT EXISTS alumnos(
  dni CHAR(9) PRIMARY KEY,
  nombre VARCHAR(30) NOT NULL,
  edad INTEGER,
  fNacimiento DATE
);
```
> También se puede identificar la clave primaria de esta manera, pero esta sintaxis no nos servirá para claves primarias compuestas, teniendo que hacerse en ese caso de la manera anterior

[Volver al índice](#%c3%8dndice)

#### Tipos de datos - Dominio
Los dominios más habituales para los atributos en PostgreSQL son los siguientes:
* Numéricos:
  * Integer: número entero, sin decimales de -2147483648 a +2147483647
  * Bigint: de -9223372036854775808 a +9223372036854775807
  * Smallint de -32768 a +32767
  * Decimal: preciso, 131072 digitos antes de la coma, 16282 dígitos después de la coma
  * Real: no preciso, máximo 6 decimales, de -2147483648 a +2147483647
  * Double: máximo 15 decimales, de -9223372036854775808 a +9223372036854775807
* Texto:
  * Char(<longitud_total>): longitud fija y limitada
  * Varchar(<longitud_maxima>): longitud variable y limitada
  * Text: longitud variable e ilimitada
* Fecha:
  * Date: dia, mes, año
  * Time: horas, minutos, segundos
  * Timestamp: date + time
* Boleano:
  * Boolean: true o false
* Dinero:
  * Money
* Direcciones de red:
  * Inet: para direcciones host IPv4 e IPv6
  * Cidr: para direcciones de red IPv4 e IPv6

[Volver al índice](#%c3%8dndice)
##### Crear dominios
Se pueden crear dominios usando los dominios base que tiene SQL. Esta es la sintaxis:
```sql
CREATE DOMAIN <nombre_dominio> [AS] <tipo_dominio>;
```
 Por ejemplo, podemos crear un dominio que usaremos para los DNI, que será un `CHAR(9)`:
```sql
CREATE DOMAIN codigo_postal AS CHAR(9);
```
Después podremos usar este dominio a la hora de crear tablas y asignar dominio a los atributos

#### Restricciones de atributos (Constraints)
Existen cuatro restricciones principalmente:
* **PRIMARY KEY**: indica que atributos de una tabla formarán la clave primaria. Esta restricción hace que estos atributos no se puedan repetir y no puedan tomar valores nulos
* **FOREIGN KEY**: indica que un atributo toma su valor de uno de los valores del atributo al que hace referencia. Se puede elegir que se hace en el caso de que el valor referenciado se modifique o se elimine, con el argumento ON UPDATE y ON DELETE respectivamente. El valor por defecto de estos argumentos es NO ACTION, no realizar ninguna acción. También se puede indicar si la coincidencia tiene que ser exacta o parcial con MATCH FULL | PARTIAL. Esta es la sintaxis:
  ```sql
  [CONSTRAINT <nombre-restriccion>]
    FOREIGN KEY (<atributos>)
      REFERENCES <nombre-tabla-referenciada>
        [(<atributos-referenciados>)]
  [MATCH FULL|PARTIAL]
  [ON DELETE CASCADE|NO ACTION|SET NULL|SET DEFAULT]
  [ON UPDATE CASCADE|NO ACTION|SET NULL|SET DEFAULT]
  ```
  > Si no se especifican los atributos referenciados se referencia a los atributos que sean clave primaria. En los paréntesis de FOREIGN KEY se pueden poner varios atributos, si todos referencian a la misma tabla
* Opciones de ON DELETE y ON UPDATE:
  * CASCADE: si el atributo referenciado se elimina o modifica también se eliminan o modifican las claves foráneas
  * NO ACTION: si el atributo referenciado se elimina o modifica no se hace nada
  * SET NULL: si el atributo referenciado se elimina o modifica los valores de las claves foráneas se ponen a NULL
  * SET DEFAULT: si el atributo referenciado se elimina o modifica los valores de las claves foráneas se ponen a un valor por defecto

* **UNIQUE**: los valores del atributo al que se aplica deben ser únicos, no podrá repetirse ninguno en toda la tabla
* **CHECK**: se especifica una condición que debe cumplirse para poder añadir datos a la tabla. El caso más habitual son las fechas. Si, por ejemplo, en la tabla hay una fecha inicio, y una fecha fin, se puede comprobar con un CHECK que fecha inicio sea anterior a la fecha fin. Ejemplo:

  ```sql
  CREATE TABLE proyecto (
    id CHAR(8),
    presupuesto MONEY NOT NULL,
    fechaInicio DATE,
    fechaFin DATE,
    CHECK (fechaInicio < fechaFin AND presupuesto > 0)
  );
  ```
[Volver al índice](#%c3%8dndice)
## Instrucción DROP
`DROP` es una instrucción sencilla, que se usa para eliminar un schema o una tabla. La sintaxis es la siguiente:
```sql
DROP SCHEMA [IF EXISTS] <nombre_del_schema>;
```
```sql
DROP TABLE [IF EXISTS] [CASCADE|RESTRICT] <nombre_de_la_tabla>;
```
> La opción `RESTRICT` no elimina la tabla si de alguno de los valores de los atributos es referenciado por otra tabla, es decir, si tiene alguna tabla dependiente. La opción `CASCADE` eliminaría la tabla y todas las tablas que dependan de la misma. La opción por defecto es `RESTRICT`

[Volver al índice](#%c3%8dndice)
## Instrucción ALTER TABLE
Con esta instrucción podremos alterar una tabla para añadir, eliminar o modificar atributos o restricciones de la misma. 
* Añadir columnas a la tabla:

  `ALTER TABLE <nombre_tabla> ADD [COLUMN] <nombre_columna> <tipo_dominio>;`
  
  
* Eliminar columnas de la tabla:
  
  `ALTER TABLE <nombre_tabla> DROP [COLUMN] <nombre_columna> [CASCADE|RESTRICT];` 
  
  > Las opciones `CASCADE` y `RESTRICT` tienen el mismo efecto que en `DROP TABLE`, pero enfocado a las columnas
* Cambiar el dominio de la columna elegida:
  
  `ALTER TABLE <nombre_tabla> ALTER [COLUMN] <nombre_columna> [SET DATA] TYPE <tipo_dominio>;`  
* Añadir la [restricción](#restricciones-de-atributos-constraints) `UNIQUE` a un atributo de una tabla:
  
  `ALTER TABLE <nombre_tabla> ADD CONSTRAINT <nombre_restriccion> UNIQUE (<atributo>);`
* Añadir a la tabla una [restricción](#restricciones-de-atributos-constraints) de tipo `CHECK`:
  
  `ALTER TABLE <nombre_tabla> ADD CHECK (<predicado>);`
* Añadir a la tabla una [restricción](#restricciones-de-atributos-constraints) de tipo `FOREIGN KEY`:
  
  `ALTER TABLE <nombre_tabla> ADD FOREIGN KEY (<atributos>) REFERENCES <nombre-tabla-referenciada>;`

  > Se pueden usar todas las opciones descritas en las [restricciones](#restricciones-de-atributos-constraints)
* Establecer que la columna indicada no admita valores nulos:
  
  `ALTER TABLE <nombre_tabla> ALTER COLUMN <nombre_columna> SET NOT NULL;`
* Eliminar la restricción indicada:
  
  `ALTER TABLE <nombre_tabla> DROP CONSTRAINT <nombre_restriccion>;` 
* Renombrar una columna:

  `ALTER TABLE <nombre_tabla> RENAME COLUMN <nombre_columna> TO <nuevo_nombre>;`
* Renombrar una tabla:

  `ALTER TABLE <nombre_tabla> RENAME TO <nuevo_nombre>;`
  
  [Volver al índice](#%c3%8dndice)

# DML - Manipulación de datos
**DML** (Data Manipulation Language) es el sub-lenguaje de SQL que agrupa los comandos necesarios para introducir, modificar y eliminar datos en una tabla de SQL
>**¡OJO!** Esta sección está documentada para el gestor de bases de datos **PostgreSQL**, las instrucciones y su sintaxis pueden ser diferentes en otro gestor de bases de datos

## Instrucción INSERT
Con esta instrucción insertaremos filas de datos en una tabla. Tendremos que indicar a que tabla se le insertan los valores, los nombres de los atributos a los que asignaremos valores y los valores que se insertan. El orden de los atributos y de los valores debe ser el mismo. La sintaxis es la siguiente:
```sql
INSERT INTO <nombre_tabla> [(<atributo1>, <atributo2>, ...)] 
(VALUES (<valor1>, <valor2>, ...) | SELECT ...);
```
> Si se omiten los atributos a los que se asignan los valores se utiliza el orden de creación de los atributos para el orden de los valores

> Si en vez de especificar los valores estos se sacan de otra tabla con un `SELECT`, la consulta deberá devolver el número de columnas adecuado y en el orden correcto

Ejemplo:
```sql
INSERT INTO alumnos (dni, nombre, clase) 
VALUES ('12345678A', 'Alicia', '2º B');
```
Se pueden añadir varias filas con una sola instrucción de la siguiente manera:
```sql
INSERT INTO alumnos (dni, nombre, clase) VALUES 
    ('12345678A', 'Alicia', '2º B'),
    ('12345678B', 'Bernardo', '2º A'),
    ('12345678C', 'Celia', '1º A');
```
Ejemplo de `INSERT` sacando los valores de otra tabla:
```sql
INSERT INTO alumnos (dni, nombre, clase) 
SELECT dni, nombre, clase
FROM nuevosAlumnos;
```
[Volver al índice](#%c3%8dndice)
## Instrucción UPDATE
Con esta instrucción podemos modificar los datos de una tabla. Se puede modificar solo una fila de datos, todas las filas o solo las especificadas. Tenemos que indicar que tabla y que atributo o atributos se van a modificar, el nuevo valor y que filas se modifican. Esta es la sintaxis:
```sql
UPDATE <nombre_tabla> 
SET <nombre_atributo1> = <nuevo_valor1>, <nombre_atributo2> = <nuevo_valor2>, ... 
[WHERE <condicion_para_escoger_filas>];
```
> Si se omite el `WHERE` se estarían escogiendo todas las filas de la tabla

Ejemplo:
```sql
UPDATE productos 
SET cantidad = 0
WHERE nombreProducto = 'Papel Higiénico';
```
> Con esta operación seleccionamos la fila de la tabla productos que corresponde al papel higienico e indicamos que se ha acabado el stock

También podemos referirnos al anterior valor del atributo para establecer su nuevo valor. En la siguiente operación se establece que el precio del producto 'Arroz' sea el mismo que tenía antes multiplicado por 1,20:
```sql
UPDATE productos 
SET precio = precio * 1.20
WHERE nombreProducto = 'Arroz';
```
> Hay que tener mucho ojo con la condición del `WHERE`, ya que si no la ponemos o ponemos una condición incorrecta, podríamos sobreescribir todas las filas de la tabla con el mismo valor

[Volver al índice](#%c3%8dndice)
## Instrucción DELETE
Con esta instrucción se pueden eliminar filas de una tabla. Se debe especificar la tabla y una condición con un `WHERE` donde se especifique que filas se van a eliminar. **Si se omite el WHERE se eliminarán todas las filas**:
```sql
DELETE FROM <nombre_tabla> [WHERE <condicion_para_escoger_filas>];
```
Ejemplo:
```sql
DELETE FROM productos WHERE numeroVentasMes < 5;
```
> Eliminamos de la tabla los productos de los que se hayan vendido menos de 5 unidades en un mes

[Volver al índice](#%c3%8dndice)