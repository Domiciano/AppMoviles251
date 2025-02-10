#Sesión 2
En esta sesión, hicimos los ejercicio de UI Basics de Jetpack Compose. Además introducimos el concepto de estado y de lambas de kotlin. Sufrimos un poco a causa de una desafortunada multiplicación por 0, pero lo logramos y pudimos explicar el funcionamiento de estados en un TextField
```kotlin
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import androidx.compose.foundation.Image
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Button
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.example.lab1.ui.theme.Lab1Theme

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            Lab1Theme {
                Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
                    var cuenta by remember {
                        mutableStateOf(0)
                    }

                    ScreenB(cuenta) {
                        cuenta = it + 10
                    }
                }
            }
        }
    }
}





@Composable
fun ScreenB(
    counter: Int,
    counterFunction: (Int) -> Unit
) {

    var email by remember {
        mutableStateOf("")
    }

    Column(modifier = Modifier.padding(32.dp)) {
        Text(text = "La cuenta va en ${counter}")
        Button(onClick = { counterFunction(counter) }) {
            Text(text = "Contar")
        }
        TextField(value = email, onValueChange = {
            email = it
        })
    }

}

```
