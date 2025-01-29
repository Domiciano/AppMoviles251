# ⚠️ Under construction
# Consumo de Rest usando MVVM

En este laboratorio trabajará en una aplicación que consume el API de deezer.

Debe hacerlo con el patrón de arquitectura MVVM. 

El trabajo se enfoca en crear la capa de Repository y Service.

Recuerde que la lógica de negocio se centra en Repository y el Service debe contener exclusivamente la forma de descargar/enviar los datos.

# Service
- Hace las llamadas reales a la red o la base de datos.
- No sabe nada de la lógica del negocio o de la aplicación.

# Repository
- Usa la capa de service, es posible que necesite lógica adicional para ser enviada y transformada a la capa de la vista
- Castea DTO en Domain Model

Al inicio, especialmente en aplicaciones simples, el Repository parece un intermediario innecesario. Sin embargo con el tiempo, tu app puede necesitar cosas como:
- Uso de una caché local.
- Retries automáticos en fallos de red.
- Transformaciones complejas de datos.
- Esas tareas no deberían estar en el Service ni en la ViewModel.
