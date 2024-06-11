# Archivo Markdown - Entregable

## Nombre del Proyecto: Proyecto Final ETL


## -- Project Status: [Completed]

## Miembros del equipo

Team Leader: [Daniel Martinez]()(https://github.com/Daniel1martinez2) Instructor: [Angela Villota]

## Other Members:

Name	Email
[Alejandra Ruiz](https://github.com/alejaguz)	@alejaguz
[Luis Felipe Montenegro](https://github.com/felipe-montenegro)	@felipe-montenegro
[Juan Camilo Vergara](https://github.com/juancamilovergaraarenas)	@juancamilovergaraarenas



## Contact

Team Leader: [Daniel Martinez]()(https://github.com/Daniel1martinez2)


## Introducción/Objetivo del Proyecto

Construir el proceso ETL utilizando Pentaho Data Integration (PDI) con el fin de obtener un archivo csv, realizando el proceso de extracción y transformación de los datos disponibles en la base de datos relacional BD Ames, en archivos csv y en MongoDB. 


## Métodos Utilizados

- PgAdmin para bases de datos relacionales (PostGres)
- MongoDB para documentos
- Pentaho para integración de Bases


## Descripción del Proyecto

1. Archivo de Inmobiliaria (BASE RELACIONAL)

![alt text](Images/image.png)

- Se carga el script para crear el DataBase y se crean las tablas vacias
- Se cargan los scripts de cada tabla y se cargan en el siguiente orden: primero, las que no tienen relaciones con otras tablas para evitar error de relaciones que puedan darse con tablas que no existan

Orden (se adjuntan algunos recortes de ejemplo):
- TypeQuality primero porque es base de otras tablas
- Mszoning segundo porque también es una tabla base de otras
- Msubclass tercero porque también es una tabla base de otras
 
 ![alt text](Images/image-1.png)

- Amsdbtemp es la cuarta

 ![alt text](Images/image-2.png)

- Salesproperty es la quinta

 ![alt text](Images/image-3.png)

- Floordetail es la última por cargar

 ![alt text](Images/image-4.png)


Luego de hacer esto entonces revisamos el diagrama relacional resultante:

 ![alt text](Images/image-5.png)

En este caso nos damos cuenta que no coincide con el diagrama relacional real y por ende revisamos el código de los scripts y no encontramos una relación creada. Así que procedemos a completar el código para generar las relaciones pertinentes.

Se crea entonces las relaciones de typequality, mszoning y mssubclass:

 ![alt text](Images/image-6.png)


Diagrama resultante del código:

 ![alt text](Images/image-7.png)





2. Carga Base de Datos 2: Infrati Test - MongoDB

 
![alt text](Images/image-8.png)


3. Carga de Base de Datos relacional BD Ames a Pentaho 

![alt text](Images/image-9.png)

4. Antes de llevar a cabo el Join completo de la base de datos relacional, se lleva a cabo el cálculo de las variables FullBath, HalfBath y Bedroom

![alt text](Images/image-24.png)

Adicionalmente, se hizo la modificación de Fecha de venta, asegurando que en la salida solo esté el mes y el año de venta

![alt text](Images/image-44.png)

5. Join de bases de datos 

Luego para practicidad del ejercicio entonces se realizó el join de la mayoría de tablas excepto amesdbtemp debido a que esta base de datos tiene muchas variables

 ![alt text](Images/image-14.png)

Luego se terminó de unificar la tabla de la base relacional con amesdbtemp para construir el primer merge y luego crear el merge completo:

 ![alt text](Images/image-15.png)


6. Carga de CSV
 

![alt text](Images/image-12.png)


7. Paso intermedio - Transformaciones

Para poder realizar las transformaciones de las bases de datos, realizamos un barrido donde identificamos la fuente y nombres de variables de la salida esperada, de la siguiente manera (donde dejamos en amarillo aquellas que requieren un cálculo):

![alt text](Images/image-13.png)





8. Se lleva al cálculo de la variable gr_live_area utilizando el calculator
 
![alt text](Images/image-16.png)

![alt text](Images/image-17.png)

![alt text](Images/image-25.png)

9. Se lleva a cabo el merge con la base de datos CSV Property

![alt text](Images/image-23.png)


10. Se llevó a cabo la transformación solicitada para la variable Year_Remod/Add

- Se filtra la información en donde la varibale Year_Remod/Add es nula

![alt text](Images/image-27.png)

- Luego, aquellas celdas nulas se imputaron con el contenido de la variable Year_built

![alt text](Images/image-28.png)

- Luego se hizo la unión entre los imputados y los que quedaron fuera del primer filtro

![alt text](Images/image-29.png)


11. En este paso se llevó a cabo la carga de las bases de datos de mongo:

- Bsmt
- Garage
- Misc
- Pool

Para cada una realizamos tres operaciones: la carga, el sort y el merge con las otras bases: 

- La carga

![alt text](Images/image-30.png)

![alt text](Images/image-33.png)

- El sort

![alt text](Images/image-31.png)

- El merge

![alt text](Images/image-32.png)

*Importante fue deseleccionar el output single JSON field, para que se transformara en formato de tabla y asi si se pudiera realizar el Merge. Para este caso, fue importante que se realizara un left outer join para que se hiciera el merge correctamente, porque las bases de mongo tenían menos valores y por eso queríamos conservar la tabla con mayor cantidad de datos y hacer el join con los menores valores de mongo.

 se validó que no existieran duplicados a la hora de realizar el Merge


![alt text](Images/image-20.png)

Al final de este proceso, las bases de mongo quedaron así:

![alt text](Images/image-34.png)

12. Luego de realizar la carga de los datos de MongoDB, se lleva a cabo la imputación de datos faltantes de la siguiente manera: NA si es cualitativa y 0 si la variable es númerica

![alt text](Images/image-35.png)

![alt text](Images/image-36.png)

13. Luego se llevó a cabo la transformación de la variable Neighborhood. En primer lugar se creó un archivo CSV que luego fue imputado al pentaho

![alt text](Images/image-37.png)

![alt text](Images/image-38.png)

14. Posterior a esto se llevó a cabo el merge entre la base de datos resultante después de realizar todo el proceso de merge de las bases de datos de mongo con el CSV cargado

![alt text](Images/image-39.png)

15. A continuación se lleva  acabo la transformación de la variable LotShape utilizando la función value mapper

![alt text](Images/image-40.png)

16. Para finalizar el proceso de tranformación y para asegurar la calidad de la salida se hizo una selección de valores en la que se realizaron los siguientes cambios:

- Se eliminaron columnas duplicadas


- Se renombró la variable Bedroom por Bedroom AbvGr

![alt text](Images/image-41.png)

- Se renombró la variable Code por Neighborhood

![alt text](Images/image-42.png)

17. Finalmente se exportó el archivo definivo utilizando la función test_file_output

![alt text](Images/image-43.png)


## Lecciones aprendidas:

- Es importante mencionar que a lo largo del flujo de trabajo siempre se llevó a cabo un proceso de ordenamiento de los datos previo a cada merge, esto con el objetivo de asegurar la calidad de los datos evitando duplicados, errores y buscanod mantener relaciones correctas entre las tablas.

- Con el propósito de reducir el exceso de pasos y  procesamientos en la herramienta Pentaho, es relevante optimizar las queries realizadas en el preprocesamientos de la carga inicial de los datos

- Reforzar que el número de filas esperadas depende de la cantidad de IDS que se tengan en total de todas las fuentes. 


