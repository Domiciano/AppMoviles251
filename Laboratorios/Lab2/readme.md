# Navigation con Compose

En este laboratorio aprenderás a usar la navegación en Jetpack Compose. El objetivo es crear una aplicación inspirada en Spotify donde el usuario puede navegar a través de las pantallas, crear playlist y agregar canciones a cada playlist.<br><br>
Consulte el <a href="https://www.figma.com/design/p0BC4jwSeRZrAfpxQ7CaJd/Login-Mobile-App-Screens-%7C-Free-(Community)?node-id=6-60&t=NJEheAiP3AIiwfaG-1">Figma</a> si requiere descargar los recursos<br><br>


El objetivo de este laboratorio, es utilizar el navigation compose.<br><br>

## 1. Esquema de navegación
Defina el esquema de navegación entre las pantallas. Debe tener claro que hay pantallas anidadas y por lo tanto `NavigationController` anidados. Observe la barra de navegación inferior, se dará una idea de cómo está ensamblada la aplicación


## 2. Edición de perfil

Al dar click en el ícono de editar, vamos a la screen de edición del perfil.
<p align="center">
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab2Image1.png" width="256" />
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab2Image2.png" width="256" /> 
</p>

En la segunda pantalla, use el botón de actualizar para cargar la imagen a partir de la URL escrita. Puede facilitar el proceso teniendo en cuenta que puede hostear imágenes en Github de modo que la URL estática de la imagen tiene la siguiente forma.
```
https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab2Image1.png
```

De este modo solo tendría que introducir `Lab2Image1.png` <br><br>

Debe importar esta librería para usar el componente AsyncImage, que le permite cargar imágenes a partir de una URL

```
implementation("io.coil-kt.coil3:coil-compose:3.0.4")
implementation("io.coil-kt.coil3:coil-network-okhttp:3.0.4")
```

## 3. Playlists
Desde esta pantalla podrá ir a crear una Playlist a través del botón de crear.

<p align="center">
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab2Image3.png" width="256" /> 
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab2Image4.png" width="256" /> 
</p>

## 4. Canciones de la playlists
Finalemente, podrá entrar al playlist dando click a uno de los items. Para efectos del ejercicio, use un item de lista mock y defina la lógica de navegación

<p align="center">
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab2Image5.png" width="256" /> 
  <img src="https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab2Image6.png" width="256" /> 
</p>

⚠️ No se sienta mal si la información no persiste, pronto lo aprenderá a hacer
