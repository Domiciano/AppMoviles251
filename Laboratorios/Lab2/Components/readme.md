
## Components

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import coil3.compose.AsyncImage
import androidx.compose.foundation.layout.Box
import androidx.compose.material.icons.outlined.Menu
import androidx.compose.material.icons.outlined.Person
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.size

import androidx.compose.material3.OutlinedTextField

import androidx.compose.material3.TextFieldDefaults
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.sp
import androidx.compose.material3.*

import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Refresh
import androidx.compose.ui.graphics.Brush
import androidx.compose.foundation.layout.Row
import androidx.compose.material.icons.filled.Add


@Composable
fun AppMusicListItem(
    imageUrl: String = "https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab2Cover.png",
    title: String = "Title",
    subtitle: String = "Subtitle",
    modifier: Modifier = Modifier
) {
    Row(
        verticalAlignment = Alignment.CenterVertically,
        modifier = modifier
            .fillMaxWidth()
            .padding(16.dp)
    ) {
        // Imagen circular
        AsyncImage(
            model = imageUrl,
            contentDescription = null,
            contentScale = ContentScale.Crop,
            modifier = Modifier
                .size(56.dp)
                .clip(CircleShape)
        )

        Spacer(modifier = Modifier.width(16.dp))

        // Contenedor de texto (título y subtítulo)
        Column(
            verticalArrangement = Arrangement.Center,
            modifier = Modifier.weight(1f)
        ) {
            Text(
                text = title,
                style = MaterialTheme.typography.bodyLarge,
                color = Color.White,
                maxLines = 1,
                overflow = TextOverflow.Ellipsis
            )

            Text(
                text = subtitle,
                style = MaterialTheme.typography.bodyMedium,
                color = Color.White,
                maxLines = 1,
                overflow = TextOverflow.Ellipsis
            )

        }
    }
}




@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun LabeledTextField(
    label: String = "Label",
    hint: String = "Hint",
    value: String,
    onValueChange: (String) -> Unit
) {
    Column(modifier = Modifier.padding(16.dp)) {
        // Label text
        Text(
            text = label,
            style = MaterialTheme.typography.bodyLarge.copy(color = Color.White),
            modifier = Modifier.padding(bottom = 8.dp)
        )

        // Text field with Material 3 styles
        OutlinedTextField(
            value = value,
            onValueChange = onValueChange,
            placeholder = {
                Text(
                    text = hint,
                    style = MaterialTheme.typography.bodyMedium.copy(color = Color.Gray)
                )
            },
            shape = RoundedCornerShape(8.dp), // Rounded corners for the border
            colors = TextFieldDefaults.colors(
                focusedContainerColor = Color(0x22FFFFFF),
                unfocusedContainerColor = Color(0x22FFFFFF),
                focusedIndicatorColor = Color(0x00FFFFFF),
                unfocusedIndicatorColor = Color(0x00FFFFFF),
                cursorColor = Color.White,
                focusedTextColor = Color.White,
                unfocusedTextColor = Color.White
            ),
            modifier = Modifier
                .fillMaxWidth()
                .background(Color.Transparent) // Transparent background
        )
    }
}





@Composable
fun ProfileCard(
    imageUrl: String = "https://raw.githubusercontent.com/Domiciano/AppMoviles251/refs/heads/main/res/images/Lab2Cover.png",
    name: String = "Name",
    username: String = "Username"
) {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier
    ) {
        // Imagen circular del perfil
        AsyncImage(
            model = imageUrl,
            contentDescription = "Profile Image",
            modifier = Modifier
                .size(240.dp) // Tamaño de la imagen
                .clip(CircleShape)
        )

        Spacer(modifier = Modifier.height(8.dp)) // Espacio entre la imagen y el texto

        // Nombre en blanco y grande
        Text(
            text = name,
            color = Color.White,
            style = androidx.compose.material3.MaterialTheme.typography.headlineSmall
        )

        Spacer(modifier = Modifier.height(4.dp)) // Espacio entre el nombre y el username

        // Nombre de usuario en gris
        Text(
            text = username,
            color = Color.Gray,
            style = androidx.compose.material3.MaterialTheme.typography.bodyMedium
        )
    }
}




@Composable
fun StatCard(
    number: String,
    label: String
) {
    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        modifier = Modifier
    ) {
        // Número grande
        Text(
            text = number,
            style = TextStyle(
                fontSize = 48.sp, // Tamaño grande para el número
                fontWeight = FontWeight.Bold, // Peso de la fuente en negrita
                color = Color.White // Color blanco para el número
            )
        )

        Spacer(modifier = Modifier.height(8.dp)) // Espacio entre el número y el label

        // Label debajo del número
        Text(
            text = label,
            style = TextStyle(
                fontSize = 12.sp, // Tamaño más pequeño para el label
                fontWeight = FontWeight.Normal, // Peso de fuente normal
                color = Color.Gray // Color gris para el label
            )
        )
    }
}




@Composable
fun LabeledTextFieldWithButton(
    label: String = "Label",
    hint: String = "Hint",
    value: String,
    onValueChange: (String) -> Unit,
    onButtonClick: () -> Unit // Acción del botón
) {
    Column(modifier = Modifier.padding(horizontal = 16.dp, vertical = 4.dp)) {
        // Etiqueta
        Text(
            text = label,
            style = MaterialTheme.typography.bodyLarge.copy(color = Color.White),
            modifier = Modifier.padding(bottom = 8.dp)
        )

        // Campo de texto con botón
        Row(
            verticalAlignment = Alignment.CenterVertically,
            modifier = Modifier.fillMaxWidth()
        ) {
            OutlinedTextField(
                value = value,
                onValueChange = onValueChange,
                placeholder = {
                    Text(
                        text = hint,
                        style = MaterialTheme.typography.bodyMedium.copy(color = Color.Gray)
                    )
                },
                shape = RoundedCornerShape(8.dp),
                colors = TextFieldDefaults.colors(
                    focusedContainerColor = Color(0x22FFFFFF),
                    unfocusedContainerColor = Color(0x22FFFFFF),
                    focusedIndicatorColor = Color(0x00FFFFFF),
                    unfocusedIndicatorColor = Color(0x00FFFFFF),
                    cursorColor = Color.White,
                    focusedTextColor = Color.White,
                    unfocusedTextColor = Color.White
                ),
                modifier = Modifier.weight(1f)
            )

            Spacer(modifier = Modifier.width(8.dp)) // Espaciado entre el campo y el botón

            // Botón de actualización
            IconButton(
                onClick = onButtonClick, // Acción al hacer clic
                modifier = Modifier.size(48.dp) // Tamaño del botón
            ) {
                Icon(
                    imageVector = Icons.Default.Refresh, // Ícono de actualización
                    contentDescription = "Actualizar",
                    tint = Color.White // Color del ícono
                )
            }
        }
    }
}




@Composable
fun TitleWithBackground(
    imageUrl: String,
    title: String,
    subtitle: String
) {
    Box(
        modifier = Modifier
            .fillMaxWidth()
            .height(250.dp) // Altura del componente
            .clip(RoundedCornerShape(bottomStart = 16.dp, bottomEnd = 16.dp)) // Bordes redondeados en la parte inferior
    ) {
        // Imagen de fondo
        AsyncImage(
            model = imageUrl,
            contentDescription = "Background Image",
            modifier = Modifier
                .fillMaxSize()
                .clip(RoundedCornerShape(bottomStart = 16.dp, bottomEnd = 16.dp)), // Aplicar bordes redondeados
            contentScale = androidx.compose.ui.layout.ContentScale.Crop // Escalar imagen para llenar el espacio
        )

        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp) // Altura del gradiente
                .background(
                    brush = Brush.verticalGradient(
                        colors = listOf(Color.Black.copy(alpha = 0.7f), Color.Transparent)
                    )
                )
                .align(Alignment.TopStart) // Posición superior
        )

        // Título y subtítulo
        Column(
            modifier = Modifier
                .align(Alignment.TopStart) // Esquina superior izquierda
                .padding(16.dp) // Espaciado interno
        ) {
            Text(
                text = title,
                style = androidx.compose.material3.MaterialTheme.typography.headlineMedium,
                color = Color.White
            )
            Text(
                text = subtitle,
                style = androidx.compose.material3.MaterialTheme.typography.bodyMedium,
                color = Color.White
            )
        }
    }
}






@Composable
fun AppBottomNavigationBar(
    selectedItem: Int = 1,
    onOptionClick: (Int) -> Unit
) {
    Box(
        modifier = Modifier
            .fillMaxWidth()
            .background(Color.Transparent)
    ) {
        // Borde superior gris
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(1.dp)
                .background(Color(0xFF3A434F))
                .align(Alignment.TopStart)
        )

        // Barra de navegación
        NavigationBar(
            containerColor = Color.Transparent,
            tonalElevation = 0.dp,
            modifier = Modifier.fillMaxWidth()
        ) {
            // Opción "Profile"
            NavigationBarItem(
                icon = {
                    Icon(
                        imageVector = Icons.Outlined.Person,
                        contentDescription = "Profile",
                    )
                },
                label = {
                    Text(
                        text = "Profile",
                    )
                },
                selected = selectedItem == 0,
                onClick = { onOptionClick(0) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = Color.White,
                    unselectedIconColor = Color(0xFF3A434F),
                    indicatorColor = Color.Transparent,
                    selectedTextColor = Color.White,
                    unselectedTextColor = Color(0xFF3A434F)
                )
            )

            // Opción "Playlists"
            NavigationBarItem(
                icon = {
                    Icon(
                        imageVector = Icons.Outlined.Menu,
                        contentDescription = "Playlists",
                    )
                },
                label = {
                    Text(
                        text = "Playlists",
                    )
                },
                selected = selectedItem == 1,
                onClick = { onOptionClick(1) },
                colors = NavigationBarItemDefaults.colors(
                    selectedIconColor = Color.White,
                    unselectedIconColor = Color(0xFF3A434F),
                    indicatorColor = Color.Transparent,
                    selectedTextColor = Color.White,
                    unselectedTextColor = Color(0xFF3A434F)
                )
            )
        }
    }
}




@Composable
fun AppFloatingButton(
    onClick: () -> Unit
) {
    FloatingActionButton(
        onClick = onClick,
        containerColor = Color(0xFF1ED760) // Fondo del botón
    ) {
        Row(
            verticalAlignment = Alignment.CenterVertically,
            modifier = Modifier.padding(horizontal = 16.dp) // Espaciado interno
        ) {
            Icon(
                imageVector = Icons.Default.Add,
                contentDescription = "Add",
                tint = Color.Black, // Color del ícono
                modifier = Modifier.size(24.dp) // Tamaño del ícono
            )
            Text(
                text = "Crear",
                color = Color.Black, // Color del texto
                fontSize = 16.sp, // Tamaño de la fuente
                modifier = Modifier.padding(start = 4.dp) // Espaciado entre ícono y texto
            )
        }
    }
}
```
