# Prácticas mecanismos avanzados de seguridad #2

## Ejercicio 2

### Anonimización de datos en reposo

Libreta de práctica para los estudiantes del curso CIB-209, Temas Especiales en Seguridad de Datos y Sistemas.

## Aviso sobre los datos

Los datos son sintéticos y no corresponden a personas ni a eventos reales. Los conjuntos vienen incluidos en los archivos eventos_seguridad.csv y empleados_rrhh.csv, junto a la libreta. Los datos ya están generados y no se calculan al ejecutar, por lo que todos los grupos ven exactamente los mismos números.

## ¿Cómo ejecutar en Binder?

1. Abrir el repositorio en Binder con el botón de abajo:

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/wrodriguez-cenfotec/ejercicio2_anonimizacion_en_reposo/HEAD?urlpath=%2Fdoc%2Ftree%2Fejercicio2_anonimizacion_en_reposo.ipynb)

2. La primera carga tarda unos minutos mientras se construye el entorno. Tener paciencia.
3. Ejecutar las celdas en orden, de arriba hacia abajo.
4. La sesión de Binder es temporal. Antes de cerrar, guardar las capturas de los resultados.

## ¿Qué técnica se usa y qué conviene recordar?

Este ejercicio no usa aprendizaje automático. Usa tres técnicas de protección de datos personales y luego dos ataques contra el resultado.

SHA-256 es una función de resumen, no un cifrado. Convierte la cédula en una cadena de longitud fija y no existe una operación que la devuelva al valor original. Eso no significa que sea irreversible en la práctica: si el atacante conoce la lista de cédulas posibles, calcula el resumen de todas y compara. Ese es el ataque de diccionario. La sal es un valor secreto que se agrega antes de calcular el resumen, de modo que quien no la conoce no puede reproducirlo.

La generalización no oculta el dato, le baja el detalle: la dirección IP pasa de identificar el equipo a identificar solo la red, y la fecha pasa de identificar el momento a identificar el mes. La supresión elimina la columna por completo.

Vocabulario que conviene tener claro:

- Resumen criptográfico: salida de longitud fija que representa un valor de entrada.
- Sal: valor secreto que se agrega antes de calcular el resumen.
- Seudonimización: sustituir el identificador directo por un código. Sigue siendo dato personal.
- Anonimización: ya no queda forma razonable de volver a la persona.
- Reidentificación: volver a asociar un registro protegido con una persona concreta.
- Fuente auxiliar: información que el atacante ya tiene, en este caso la tabla de personal.

## ¿Qué hace la libreta?

- Carga 1500 eventos de acceso al repositorio documental y la tabla de 40 personas de recursos humanos, que cumple el papel de fuente auxiliar del atacante.
- Aplica al conjunto el método de protección configurado: seudonimización con SHA-256 sin sal, seudonimización con SHA-256 con sal, o generalización con supresión del identificador directo.
- Ajusta la precisión de la dirección IP y de la fecha según la configuración.
- Ejecuta tres consultas de investigación sobre la tabla protegida y reporta si cada una funciona, queda parcial o falla.
- Ejecuta dos ataques: uno de diccionario contra el identificador seudonimizado y uno de cruce con la tabla de personal usando la dirección de red, e informa cuántas personas quedan reidentificadas y en cuánto tiempo.

## ¿Cómo se leen las salidas?

Este ejercicio no produce gráficos, produce tablas. Hay tres tipos de salida que se leen distinto.

La tabla protegida sirve para ver qué columnas sobrevivieron y con cuánto detalle quedaron.

El estado de las tres consultas de investigación tiene tres valores posibles: funciona, parcial o falla. Parcial significa que la consulta corre pero ya no entrega el detalle que una investigación necesita.

El resultado de los dos ataques se lee en cantidad de personas reidentificadas sobre 40, más el tiempo que tardó el ataque de diccionario.

## ¿Qué debe modificar el grupo?

Solo la celda CONFIGURACIÓN. Los parámetros son metodo_de_proteccion, precision_de_ip y precision_de_fecha.

| Corrida | metodo_de_proteccion | precision_de_ip | precision_de_fecha |
| --- | --- | --- | --- |
| 1 | seudonimizacion_sin_sal | completa | exacta |
| 2 | seudonimizacion_con_sal | completa | exacta |
| 3 | generalizacion | segmento | mes |

Efecto de cada parámetro. metodo_de_proteccion cambia qué tan reversible queda el dato: sin sal el resumen criptográfico es reproducible por cualquiera que tenga la lista de cédulas, y ese universo es pequeño, así que la protección es aparente; con sal ese ataque se cierra, pero el dato sigue vinculable dentro de la organización y por lo tanto sigue siendo dato personal. precision_de_ip y precision_de_fecha controlan cuánto detalle se conserva: al truncar la dirección al segmento se pierde el equipo exacto y se mantiene la red, y al reducir la fecha al mes se conserva la estadística y se pierde la secuencia de eventos, que es lo que necesita una investigación.

## ¿Qué se entrega?

Hoja de evidencia del ejercicio 2, con los valores de las tres corridas y cuatro capturas. El detalle está en el documento Practicas_Mecanismos_Avanzados_Seguridas_2.docx de la práctica.

## Referencias

- https://pandas.pydata.org/docs/user_guide/index.html
- https://www.prodhab.go.cr/
