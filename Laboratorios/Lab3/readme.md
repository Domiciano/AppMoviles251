# Listas
El objetivo de este laboratorio es experimentar el funcionamiento de los ViewModel en un esquema complejo de navegación. Si bien las aplicaciones en Jetpack Compose son SAA (Single Activity Application), verá que el comportamiento de los ViewModel intentan optimizar la memorización de estados.

<p align="center">
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab3Cover.png" width="512" />
</p>


## Icesi Music strikes back

Vamos a retomar el trabajo anterior donde definimos el esquema de navegación. Ahora tendrá que usar la capa de ViewModel y modelar los estados propios para las pantallas. Además tendrá que recordar cómo hacer paso de parámetros entre pantallas para lograr un flujo de información que posibilte el almacenamiento de los datos que se crean en la aplicación.<br><br>
⚠️ Nuevamente, no se desespere si los datos se borran cuando cierra la aplicación. Aún no sabe ninguna forma de persistencia, pero la sabrá y será un grande

## 1. ProfileScreen y ProfileEditScreen
Modele el estado de la pantalla de Perfil. Cree el ViewModel `ProfileViewModel` y vincúlelo a la vista. Tenga en cuenta que esta pantalla representa una instancia de una clase, más no un grupo de instancias.
<p align="center">
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab3Image1.png" width="256" />
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab3Image2.png" width="256" /> 
</p>

## 2. PlaylistsScreen y NewPlaylistScreen
Modele el estado de la pantalla de Perfil. Cree el ViewModel `PlaylistsViewModel` y vincúlelo a la vista. Tenga en cuenta que `PlaylistsScreen` muestra una lista de items. Aquí debe aplicar `LazyColum`.
<p align="center">
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab3Image1.png" width="256" />
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab3Image2.png" width="256" /> 
</p>

## 3. PlaylistDetailScreen y NewSongScreen
En esta vista se muestra una lista de canciones. Cree el ViewModel `PlaylistDetailViewModel` y vincúlelo a la vista. Verifique si la pantalla NewSongScreen debe tener estado almacenado en ViewModel o no.
<p align="center">
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab3Image1.png" width="256" />
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab3Image2.png" width="256" /> 
</p>