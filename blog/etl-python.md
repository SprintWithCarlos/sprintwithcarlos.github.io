# Spotify Data ETL with Python
_Wed 03 2021_
\#datascience

Based on [Karolina Sowinska](https://github.com/karolina-sowinska) tutorial
Link to [repo](https://github.com/SprintWithCarlos/etl-python-spotify). You can clone or fork it at your will.

## Preparation
* [ ] Getting Spotify token on this [link](https://developer.spotify.com/console/get-recently-played/)  
## Extract
Extract means to download or obtain data from vendor. In this case we will get the data by calling an API.
We will use requests library to call Spotify API. **Recommended**: Create a virtual environment for dependencies using conda. Activate it and install dependencies
```
conda create -n etl python=3 anaconda -y
source activate etl
conda install sqlalchemy pandas requests -y
conda install -c conda-forge python-dotenv schedule -y
```
If you get this error:
```
{'error': {'status': 401, 'message': 'The access token expired'}}
````
Just reissue the token at Spotify. It expires very fast.
Once you run the script you will have a data frame with the first 20 songs you listened to in the last 24 hours.
Example:
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
Once you got this data the extract stage is completed.
## Load
Load means validate the data to ensure quality.
We will create a function to which we will pass a data frame as parameter to check:
* If dataframe is empty
* That there are no duplicates
* No nulls
* No data out of the specific time frame (last 24 hours)
If everything ok, the check will return the following message:
```
Data valid, proceed to Load stage
```
## Extract
The goal of this stage is to save the data in our database, which in this case is SQLite.
Once completed this step you can check table "my_played_tracks":
```bash
sqlite3
```
```sqlite
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
With this last step our ETL is completed. We have downloaded, validated and saved data from a vendor to our database. There are multiple variations of this process, please let me know if you want to explore a different setup. i.e saving to a non relational database in the cloud.

## Optional: Scheduling the ETL Process with Cron Jobs
Instead of triggering the code every 24 hours we can automate this step.
In this particular case we have use the schedule library, so while the program is running it will trigger each day at 00:00. Details in the code.

