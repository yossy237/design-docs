# TITULO DEL DESIGN DOC
Link: [Link a este design doc](#)

Author(s): Yossy Leydi Gatica Martinez 

Status: [Draft, Ready for review, In Review, Reviewed]

Ultima actualización: 2025-03-28

## Contenido
- Goals (Objetivos)
  
  *Presentar al artista y sus especializaciones.
       .Mostrar claramente la especialización del artista en realismo y sombras.
       .Establecer un primer contacto amigable con el cliente.

  *Promover los servicios del estudio.
       .Destacar los dos principales servicios: tatuajes realistas y retratos a lápiz.

  *Facilitar la interacción del usuario con enlaces a redes sociales.
        .Incluir botones o enlaces directos a Instagram, Facebook, y otros perfiles relevantes del artista.

  *Agendar citas fácilmente para tatuajes o retratos.
        .Permitir a los usuarios seleccionar fecha, hora y el servicio deseado.

  *Enviar recordatorios sobre citas y cuidados posteriores.
        .Notificar a los clientes de las citas programadas y enviar recordatorios sobre cuidados después del tatuaje.

  *Informar sobre promociones especiales.
         .Notificar a los usuarios sobre cualquier promoción o evento especial del estudio.
  
-Non-Goals
  *Atender consultas de salud o cuidados médicos.
        .No se dará asesoría médica sobre el tatuaje (por ejemplo, infecciones o reacciones alérgicas).

  *Vender productos relacionados al tatuaje.
        .El bot no se dedicará a la venta de productos, solo a los servicios de tatuajes y retratos.
- Background (Antecedentes)
   *El estudio de tatuajes actualmente gestiona citas manualmente
  mediante llamadas y mensajes, lo que genera errores y demoras en las reservas.
  
  *Estudio de Tatuajes y Retratos Realistas: Dirigido por un tatuador especializado en realismo y sombras.

  *Objetivo del Bot: Automatizar el proceso de interacción con los clientes, facilitando la programación de citas, la consulta de 
     servicios, y la promoción de las redes sociales del artista.

  *Artista: se especializa en tatuajes realistas, retratos a lápiz, y diseño con sombras.
  
- Overview (Resumen General)
*Se propone desarrollar un bot de Telegram: El bot de Telegram estara diseñado para proporcionar una experiencia eficiente y amigable a los clientes del estudio. Ofrecerá opciones claras para explorar los servicios, agendar citas y recibir actualizaciones importantes sobre citas y promociones. Además, incluirá enlaces a redes sociales para que los clientes puedan seguir el trabajo del artista y ver ejemplos previos.

- Detailed Design
  - Solucion 1
    - Frontend
    - Backend
  - Solucion 2
    - Frontend
    - Backend
- Consideraciones
- Métricas

## Links
- [Un link](#)
- [Otro link](#)

## Objetivo
*  Que el cliente pueda elegir el servicio (TATUAJES, RETRATOS A LAPIZ)
*  Que el cliente pueda agendar su cita
*  TATUAJES
   -ENTREVISTA PREVIA AL TATUAJE(IMPERTENSO, DIABETICO, ALGUNA ENFERMEDAD)
   -
  -TAMAÑO( CHICO, MEDIANO, GRANDE)
  -DISEÑO (SI,NO)
   
  

Actualmente, ninguna app ofrece una forma de transferir playlists desde una plataforma a la otra

## Goals
- Transferir playlists de Spotify a YouTube Music
- Ofrecer opciones para elegir el reemplazo de una canción si no se encuentra el match
## Non-Goals
- Transferir playlists de YouTube Music a Spotify
- Mantener en sincronía ambas playlist

## Background
Hace poco me cambié de Spotify a YouTube Music, y no pude transferir mis playlists porque ambas apps no ofrecen herramientas para ello

Hay sitios web que ofrecen este feature, pero mi segunda intención es aprender a usar las APIs de YT Music y Spotify

## Overview
Necesitamos una API que convierta una playlist de un usuario de Spotify a una playlist de YouTube Music

Cada playlist tiene un id, y podemos consultar todas sus canciones a través de la API oficial usando ese id

Spotify ofrece una [API](https://developer.spotify.com/documentation/web-api/reference/#/operations/get-list-users-playlists) que podemos usar para obtener:
- Obtener las playlists de un user
- Obtener las canciones de esa playlist

Por el otro, YouTube Music no ofrece una **API pública**, pero existe una [API no oficial](https://ytmusicapi.readthedocs.io/en/latest/) que provee los métodos necesarios para crear una playlist, los cuales son:
- Crear playlist
- Buscar canción
- Añadir canción a la playlist

## Detailed Design

## Solución 1

## Spotify
### Obtener playlist del usuario
A través del endpoint */users/{user_id}/playlists* podemos obtener todas las playlist del usuario
Cuando el user seleccione una playlist, guardamos el ID

### Obtener canciones de la playlist
Con la plalist ID, a través del endpoint */playlists/{playlist_id}/tracks* podemos obtener todas las canciones
El endpoint regresa un resultado con la siguiente forma:
```json
{
  "href": "https://api.spotify.com/v1/me/shows?offset=0&limit=20\n",
  "items": [
    {}
  ],
  "limit": 20,
  "next": "https://api.spotify.com/v1/me/shows?offset=1&limit=1",
  "offset": 0,
  "previous": "https://api.spotify.com/v1/me/shows?offset=1&limit=1",
  "total": 4
}
```
Las canciones están en el campo **items**, la cual es una lista de objetos que representan a las canciones

## YouTube Music
YouTube Music no cuenta con una API oficial. Pero existe una librería hecha en Python que provee acceso a esta API.

[Librería](https://ytmusicapi.readthedocs.io/en/latest/)

### Crear playlist
Podemos crear una playlist usando la siguiente función de la librería

```python
YTMusic.create_playlist(title: str, description: str, privacy_status: str = 'PRIVATE', video_ids: List[T] = None, source_playlist: str = None) → Union[str, Dict[KT, VT]]
```

### Buscar canciones de Spotify en YouTube Music
Podemos usar la función de search para buscar cada canción
Esto significa que por *n canciones*, se tienen que hacer *n llamadas*

```python
# Snippet
YTMusic.search(query: str, filter: str = None, scope: str = None, limit: int = 20, ignore_spelling: bool = False) → List[Dict[KT, VT]]
```

```python
# Ejemplo
youtubeSongs = []
for item in canciones:
    # crear el query de la canción
    cancion = item['title'] + " " + item['artist'] + " " + item['album']
    # buscar la canción y agregar el primer resultado
    youtubeSongs.append(
        Songs.YTMusic.search(
        query: cancion,
        filter: "songs",
        limit: 20,
        ignore_spelling: True)[0]
    )
```

### Guardar canción en playlist

```python
def getId(song):
    return song['id']

youtubeSongsIds = map(addition, youtubeSongs)

YTMusic.add_playlist_items(playlistId: playlistId, videoIds: youtubeSongsIds, source_playlist: None, duplicates: False)
```

## Solucion 2
Podemos reusar los mismos métodos de la solución 1, con los siguientes cambios

### Buscar canciones de Spotify en YouTube Music
En lugar de agregar la primer opción de la búsqueda inmediatamente, podemos ofrecer todas las opciones al user para que elija cual es el mejor match de la canción

Esto implica ofrecer una UI donde:
- Se muestren todas las opciones
- Se puedan reproducir cada opción para que el user elija cual es la adecuada para la playlist

Esto necesitaría otro design doc enfocado a la UI si se elige esta solución

## Consideraciones
- La librería solo esta en Python. Tal vez podemos entender como funciona para implementarla por nuestra cuenta en otro lenguaje si es necesario

## Métricas
- Checar Web Vitals para entender si la app carga de forma adecuada y tiene buen performance
- Validar cuantos usuarios terminan el proceso de migración de una playlist
