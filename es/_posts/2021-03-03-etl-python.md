---
layout: post
title: ETL de datos de Spotify con Python
tag: datascience
---


{{page.tag}}



Basado en el tutorial de [Karolina Sowinska](https://github.com/karolina-sowinska)  
Link a mi [repo](https://github.com/SprintWithCarlos/etl-python-spotify). Puedes clonar o hacer fork.

## Preparation
* [ ] Obtén el token de Spotify en este [link](https://developer.spotify.com/console/get-recently-played/)  

## Extraer
Extraer significa descargar u obtener datos del proveedor. En este caso, obtendremos los datos llamando a una API. Usaremos requests library para llamar a la API de Spotify. **Recomendado**: crea un entorno virtual para dependencias usando conda. Activarlo e instalar dependencias
```
conda create -n etl python=3 anaconda -y
source activate etl
conda install sqlalchemy pandas requests -y
conda install -c conda-forge python-dotenv schedule -y
```
Si se genera este error:
```
{'error': {'status': 401, 'message': 'The access token expired'}}
````
Simplemente vuelve a generar el token en Spotify. Caduca muy rápido. Una vez que ejecutes el script, tendrás un data frame con las primeras 20 canciones que escuchaste en las últimas 24 horas. Ejemplo:
```
                   song_name  artlist_name                 played_at   timestamp
0   Ahora - Directo Acústico        Estopa  2021-03-04T16:51:58.535Z  2021-03-04
1               Just A Lover  Hayden James  2021-03-03T19:48:52.589Z  2021-03-03
2                       Nanã    Polo & Pan  2021-03-03T19:46:01.361Z  2021-03-03
3             YOU'RE THE ONE    KAYTRANADA  2021-03-03T19:42:50.098Z  2021-03-03
4       Can't Do Without You       Caribou  2021-03-03T19:38:31.760Z  2021-03-03
5             Feel Like I Do    Disclosure  2021-03-03T19:34:34.860Z  2021-03-03
6                  Hong Kong    C. Tangana  2021-03-03T19:31:10.274Z  2021-03-03
7                 Los Tontos    C. Tangana  2021-03-03T19:27:25.438Z  2021-03-03
8            Cuándo Olvidaré    C. Tangana  2021-03-03T19:24:12.671Z  2021-03-03
9                    CAMBIA!    C. Tangana  2021-03-03T19:20:40.938Z  2021-03-03
10       Muriendo De Envidia    C. Tangana  2021-03-03T19:17:33.074Z  2021-03-03
11              Te Olvidaste    C. Tangana  2021-03-03T19:13:30.174Z  2021-03-03
12         Un Veneno - G-Mix    C. Tangana  2021-03-03T19:10:22.709Z  2021-03-03
13                   Nominao    C. Tangana  2021-03-03T19:05:43.191Z  2021-03-03
14              Ingobernable    C. Tangana  2021-03-03T19:02:46.900Z  2021-03-03
15           Párteme La Cara    C. Tangana  2021-03-03T18:59:39.944Z  2021-03-03
16               Nunca Estoy    C. Tangana  2021-03-03T18:56:52.031Z  2021-03-03
17            Comerte Entera    C. Tangana  2021-03-03T18:54:09.563Z  2021-03-03
18   Tú Me Dejaste De Querer    C. Tangana  2021-03-03T18:50:13.927Z  2021-03-03
19        Demasiadas Mujeres    C. Tangana  2021-03-03T18:46:55.615Z  2021-03-03
```
Una vez obtengas estos datos, habrás completado la etapa de extracción.
## Transformar (Transform)
Transformar significa validar los datos para garantizar la calidad. Crearemos una función a la que le pasaremos un data frame como parámetro para comprobar:

* Si el data frame está vacío
* Que no hay duplicados
* Que no hay nulos
* No hay datos fuera del período de tiempo específico (últimas 24 horas) 

Si todo está bien, la verificación devolverá el siguiente mensaje:
```
Datos válidos, pasar a la etapa de carga
```
## Cargar (Load)
El objetivo de esta etapa es guardar los datos en nuestra base de datos, que en este caso es SQLite. Una vez completado este paso, puede consultar la tabla "my_played_tracks":

```bash  
sqlite3
```
```bash  
sqlite > .open my_played_tracks.sqlite
sqlite > select * from my_played_tracks;
Just A Lover|Hayden James|2021-03-03T19:48:52.589Z|2021-03-03
Nanã|Polo & Pan|2021-03-03T19:46:01.361Z|2021-03-03
YOU'RE THE ONE|KAYTRANADA|2021-03-03T19:42:50.098Z|2021-03-03
Can't Do Without You|Caribou|2021-03-03T19:38:31.760Z|2021-03-03
Feel Like I Do|Disclosure|2021-03-03T19:34:34.860Z|2021-03-03
Hong Kong|C. Tangana|2021-03-03T19:31:10.274Z|2021-03-03
Los Tontos|C. Tangana|2021-03-03T19:27:25.438Z|2021-03-03
Cuándo Olvidaré|C. Tangana|2021-03-03T19:24:12.671Z|2021-03-03
CAMBIA!|C. Tangana|2021-03-03T19:20:40.938Z|2021-03-03
Muriendo De Envidia|C. Tangana|2021-03-03T19:17:33.074Z|2021-03-03
Te Olvidaste|C. Tangana|2021-03-03T19:13:30.174Z|2021-03-03
Un Veneno - G-Mix|C. Tangana|2021-03-03T19:10:22.709Z|2021-03-03
Nominao|C. Tangana|2021-03-03T19:05:43.191Z|2021-03-03
Ingobernable|C. Tangana|2021-03-03T19:02:46.900Z|2021-03-03
Párteme La Cara|C. Tangana|2021-03-03T18:59:39.944Z|2021-03
```

Con este último paso se completa nuestro ETL. Hemos descargado, validado y guardado datos de un proveedor en nuestra base de datos. Hay varias variaciones de este proceso, si desea explorar una configuración diferente puedes consultarme según explico en [Contacto]({{site.url}}/es/contacto). Por ejemplo, guardar en una base de datos no relacional en la nube.

## Opcional: programar el proceso ETL con trabajos cron
En lugar de correr el código cada 24 horas, podemos automatizar este paso. En este caso particular, usamos shcedule library, de tal manera que mientras el programa se esté ejecutando, se activará cada día a las 00:00. Detalles en el código.

## Happy Coding!  👩‍💻 👨‍💻