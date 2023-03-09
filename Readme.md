# Práctica Docker + Python

## Creación de la imagen "youtubeimagen:miscript"
Esta imagen será la encargada de que al ejecutarla se muestre la descripción de el vídeo que se le pasa.
Para ello crearemos el fichero _miscript.py_ y lo llenaremos con los siguientes datos donde le pasamos el
vídeo y le pedimos que nos sace los datos como el título, las visitas, la descripción, etc...

~~~
from pytube import YouTube

#link = input("Enter the link: ")
yt = YouTube("https://www.youtube.com/watch?v=34GJ9ZMAKqA&t=3s&ab_channel=PlayStationEspa%C3%B1a")

#Title of video
print("Title: ",yt.title)
#Number of views of video
print("Number of views: ",yt.views)
#Length of the video
print("Length of video: ",yt.length,"seconds")
#Description of video
print("Description: ",yt.description)
#Rating
print("Ratings: ",yt.rating)
~~~

Ahora debemos crear el "_Dockerfile_" en el que debemos poner  siguiente:

Para poder asignar la carpeta en la que tenemos el script e instalar el pytube y pasarle el script.
(Las líneas comentadas no son necesarias)

~~~
FROM python:3

WORKDIR /usr/src/app

#COPY requirements.txt ./

#RUN pip install --no-cache-dir -r requirements.txt

RUN pip install pytube

COPY ./app /usr/src/app

CMD ["python", "miscript.py"]
~~~

Y con esto ya tendríamos la imagen lista la creamos con el siguiente comando:

~~~
docker build -t youtubeimagen:miscript .
~~~

Si ahora hacemos el docker run, se ejecutará la imagen y nos mostrará los datos del vídeo que le pasamos anteriormente

~~~
docker run youtubeimagen:miscript
~~~

## Creación de la imagen "youtubeimagen:latest"
Ahora creamos la segunda imagen a la que solamente le meteremos el python y el pytube, sin pasarle nada más
para que se pueda ejecutar luego el script que se quiera.

Para esto necesitaremos un crear un _**docker-compose.yml**_ y poner lo siguente:
~~~
services:
  python:
    image: youtubeimagen:latest
    volumes:
      - ./app:/usr/src/app
    stdin_open: true
    tty: true
    command: ['python', 'latest.py']
~~~

Y también crearemos el _latest.py_ donde pondremos un simple mensaje de prueba

~~~
print("Hola mundo")
~~~

Y iremos al _Dockerfile_ que creamos antes y comanteremos la última línea del comando, quedando de la siguiente forma:

~~~
FROM python:3

WORKDIR /usr/src/app

#COPY requirements.txt ./

#RUN pip install --no-cache-dir -r requirements.txt

RUN pip install pytube

COPY ./app /usr/src/app

#CMD ["python", "miscript.py"]
~~~

Y ahora al igual que el anterior creamos la imagen

~~~
docker build -t youtubeimagen:latest .
~~~

Ahora podemos hacer _docker run -it youtubeimagen:latest_ y nos abriará la consola de python o podemos hacer
_docker-compose up_ y se noms mostrará la el mensaje de prueba que pusimos en el _latest.py_


## Subir las imágenes a dockerhub

Primeramente nos crearemos la cuenta en dockerhub y creareos un repositorio. Ahora iremos al terminal y
y haremos un _docker login_ para registrarnos.

Una vez registrados subiremos las imágenes de la siguiente forma:

~~~
docker tag youtubeimagen:latest hugocea/appyoutube:latest
docker push hugocea/appyoutube:latest

docker tag youtubeimagen:miscript hugocea/appyoutube:miscript
docker push hugocea/appyoutube:miscript
~~~

![img](https://github.com/HugoCea/ProyectoDHCP/blob/master/imagenes/imagenes.png)