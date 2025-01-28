## Screens

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.outlined.Edit
import androidx.compose.material3.Button
import androidx.compose.material3.ButtonDefaults
import androidx.compose.material3.Icon
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import coil3.compose.AsyncImage


@Composable
fun App(){
    Scaffold(
        floatingActionButton = {
            AppFloatingButton {}
        },
        bottomBar = { AppBottomNavigationBar {} }
    ) { innerPadding ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .background(Color.Black)
                .padding(innerPadding)
        ) {
            PlayListDetail()
        }
    }
}

@Composable
fun PlayListDetail() {
    Column(
        modifier = Modifier.fillMaxSize()
    ) {
        TitleWithBackground(
            imageUrl = "https://mir-s3-cdn-cf.behance.net/project_modules/max_1200/fcb2c7102027459.5f2cc34b5cebc.png",
            title = "Playlist de prueba",
            subtitle = "Descripción, si aplica"
        )
        for (i in 1..<4) {
            AppMusicListItem()
        }
    }
}

@Composable
fun PlaylistScreen() {
    Column(
        modifier = Modifier.fillMaxSize()
    ) {
        Text(text = "Playlist", fontSize = 24.sp, color = Color.White)
        for (i in 1..<4) {
            AppMusicListItem()
        }
    }
}

@Composable
fun ProfileScreen() {
    Box{
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(300.dp)
                .background(
                    brush = Brush.verticalGradient(
                        colors = listOf(Color(0xFF00667B).copy(alpha = 0.7f), Color.Transparent)
                    )
                )
                .align(Alignment.TopStart)
        )
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Row {
                Spacer(modifier = Modifier.weight(1f))
                Button(onClick = { /*TODO*/ }, colors = ButtonDefaults.buttonColors(
                    containerColor = Color.Transparent
                )) {
                    Icon(
                        imageVector = Icons.Outlined.Edit,
                        contentDescription = "Profile",
                        tint = Color.White
                    )
                }
            }
            ProfileCard()
            Row(modifier = Modifier.fillMaxSize(), horizontalArrangement = Arrangement.SpaceAround) {
                StatCard(number = "16", label = "PLAYLISTS")
                StatCard(number = "58", label = "FOLLOWERS")
                StatCard(number = "43", label = "FOLLOWING")
            }
        }
    }
}

@Composable
fun NewPlaylist() {
    Column (horizontalAlignment = Alignment.CenterHorizontally){
        TitleWithBackground(
            imageUrl = "https://mir-s3-cdn-cf.behance.net/project_modules/max_1200/fcb2c7102027459.5f2cc34b5cebc.png",
            title ="Playlist",
            subtitle ="Nueva Playlist"
        )
        LabeledTextField(value = "Alfa") {}
        LabeledTextFieldWithButton(value = "Beta", onValueChange = {}, onButtonClick = {})
        LabeledTextField(value = "Gamma") {}
        Button(onClick = { /*TODO*/ }, colors = ButtonDefaults.buttonColors(
            containerColor = Color(0xFF1ED760)
        )) {
            Text(text = "Crear playlist", color = Color.Black)
        }
    }
}


@Composable
fun NewSong() {
    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        Text(
            text = "Agrega tu canción",
            fontSize = 24.sp,
            color = Color.White,
            modifier = Modifier.fillMaxWidth()
        )
        Text(
            text = "Escribe los datos de la canción",
            fontSize = 12.sp,
            color = Color.White,
            modifier = Modifier.fillMaxWidth()
        )

        AsyncImage(
            model = "https://mir-s3-cdn-cf.behance.net/project_modules/max_1200/fcb2c7102027459.5f2cc34b5cebc.png",
            contentDescription = "Profile Image",
            modifier = Modifier
                .size(240.dp) // Tamaño de la imagen
                .clip(CircleShape)
        )

        LabeledTextField(value = "Alfa") {}
        LabeledTextField(value = "Gamma") {}
        LabeledTextFieldWithButton(value = "Beta", onValueChange = {}, onButtonClick = {})
        Button(
            onClick = { /*TODO*/ }, colors = ButtonDefaults.buttonColors(
                containerColor = Color(0xFF1ED760)
            )
        ) {
            Text(text = "Agregar canción", color = Color.Black)
        }
    }
}

@Composable
fun EditProfileScreen() {
    Box {
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(300.dp)
                .background(
                    brush = Brush.verticalGradient(
                        colors = listOf(Color(0xFF00667B).copy(alpha = 0.7f), Color.Transparent)
                    )
                )
                .align(Alignment.TopStart)
        )
        Column(horizontalAlignment = Alignment.CenterHorizontally) {
            Spacer(modifier = Modifier.height(24.dp))
            AsyncImage(
                model = "https://mir-s3-cdn-cf.behance.net/project_modules/max_1200/fcb2c7102027459.5f2cc34b5cebc.png",
                contentDescription = "Profile Image",
                modifier = Modifier
                    .size(240.dp) // Tamaño de la imagen
                    .clip(CircleShape)
            )
            LabeledTextField(value = "Alfa") {}
            LabeledTextField(value = "Gamma") {}
            LabeledTextFieldWithButton(value = "Beta", onValueChange = {}, onButtonClick = {})
            Button(
                onClick = { /*TODO*/ }, colors = ButtonDefaults.buttonColors(
                    containerColor = Color(0xFF1ED760)
                )
            ) {
                Text(text = "Guardar", color = Color.Black)
            }
        }
    }
}
```
