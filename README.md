# umc-pre803-tarea-3
Red neuronal para predecir presencia o ausencia de enfermedad cardíaca. Hecho como tercera tarea de Programación Emergente (PRE803). 


## Equipo

- Jesús Pacheco (V-23.597.642)
- Miguelángel Chávez (V-25.770.050)
- Samuel Ochoa (V-27.225.685)
- Wuilker Álvarez (V-26.440.949)


## Instrucciones de uso

Después de clonar el repositorio e instalar las librerías requeridas (véase `requirements.txt`), abrir la libreta interactiva `umc-pre803-tarea-3-local.ipynb` con Jupyter y ejecutar todas las celdas:

```bash
jupyter notebook umc-pre803-tarea-3-local.ipynb
```

La última celda ejecuta una función de "diagnóstico interactivo" que permite al usuario ingresar los datos de un paciente (imaginario), y esos datos son pasados al modelo de red neuronal para producir la predicción de si el paciente tiene una enfermedad cardíaca o no.

Alternativamente, en lugar de correr la libreta interactiva localmente, puede utilizar la [libreta original hecha en Google Colab](https://colab.research.google.com/drive/1ez_POXukebKmZn5Ftg-MqV2ztBLqVK1V?usp=sharing). El código es el mismo excepto que el archivo de datos CSV se descarga de internet en lugar de cargarlo localmente.


## Conjunto de datos utilizados

Se utiliza el conjunto de datos [Heart Disease Data Set](https://archive.ics.uci.edu/ml/datasets/heart+Disease) proveído por el [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/index.html). El conjunto de datos incluye datos recopilados en varios centros médicos de Suiza, Hungría y Estados Unidos. En este caso solo se utilizan los datos recopilados en Cleveland (Estados Unidos), ya que ese es el único archivo "procesado".

La tabla de datos utilizada tiene solo 14 atributos:

- `age`: Edad.
- `sex`: Sexo.
- `cp`: Tipo de dolor de pecho.
- `trestbps`: Presión sanguínea en reposo en mmHg.
- `chol`: Colesterol sérico en mg/dl.
- `fbs`: Azúcar sanguínea en ayuna mayor a 120 mg/dl.
- `restecg`: Resultados electrocardiográficos en reposo.
- `thalach`: Máxima frecuencia cardíaca alcanzada.
- `exang`: Angina de pecho inducida por ejercicio
- `oldpeak`: Depresión de ST inducida por ejercicio respecto al reposo.
- `slope`: Pendiente del segmento ST más alto durante el ejercicio.
- `ca`: Número de vasos sanguíneos mayores coloreados por fluoroscopia.
- `thal`: Diagnóstico de talasemia (un tipo de anemia hereditaria)
- `target`: Variable objetivo (diagnóstico de enfermedad cardíaca)

Estos atributos son renombrados para que sus nombres sean más descriptivos. El tipo de datos y los valores posibles (según aplique) para cada atributo se explican en el código y en el cuestionario de diagnóstico interactivo.


## Procedimiento

### Exploración y preparación de datos

Después de renombrar los atributos de la tabla, se muestran las características de sus columnas y una muestra de elementos. Posteriormente, se dibuja un gráfico de correlación entre ellos para tener una idea general de cuáles atributes influyen más en el diagnóstico de enfermedad cardíaca. El atributo de diagnóstico de talasemia es el que tiene más correlación con la variable objetivo (0.48), mientras que el atributo de máxima frecuencia cardíaca alcanzada es el que tiene menos correlación con ella (-0.39).


### Preprocesamiento de datos

Una vez hecho esto, se pasa a realizar el verdadero preprocesamiento de los datos. Primero, se separan los datos en un conjunto para el entrenamiento de la red neuronal (80%) y uno para la evaluación de esta (20%); además, se separan las respectivas columnas de la variable objetivo de ambas tablas de datos, para poder utilizarlas como etiquetas de los valores esperados de cada registro.

Luego, se construyen objetos de entrada de datos TensorFlow/Keras para cada atributo, realizando diferente preprocesamiento para cada tipo de dato. Los atributos binarios simplemente se convierten a vectores de entrada de valor entero. Los atributos numéricos se convierten a punto flotante, se apilan, y se pasan por una capa de normalización. Los atributos categóricos numéricos y de texto se pasan por una capa de conversión apropiada para convertir sus valores a conjuntos de bits [*one-hot*](https://es.wikipedia.org/wiki/One-hot).

Para finalizar la fase de preprocesamiento, se toman las entradas originales y las resultantes para construir un modelo de preprocesador reutilizable. Además, se genera un diagrama de visualización de las capas y conexiones de dicho modelo, y se muestra un ejemplo de cómo quedaría un conjunto de datos de entrada después de pasar por el preprocesador.

### Construcción y entrenamiento del modelo

Primero, se construye el cuerpo del modelo de red neuronal, definiendo la secuencia de capas que lo componen excluyendo la capa de entrada (definida por el preprocesador). En este caso, tenemos una capa densa de 20 neuronas con activación [ReLU](https://en.wikipedia.org/wiki/Rectifier_(neural_networks)), seguida de una capa de abandono con tasa del 20% para evitar que el modelo presente _overfitting_, luego una capa densa de 40 neuronas con activación ReLU, y por último la capa de salida de una sola neurona con activación [sigmoide](https://en.wikipedia.org/wiki/Sigmoid_function), que corresponde al valor binario que tratamos de predecir (diagnóstico de enfermedad cardíaca).

Una vez construido el cuerpo del modelo, se aplica la función del preprocesador a las entradas, y luego se aplica la función del cuerpo del modelo a las entradas preprocesadas. El resultado de esto se junta con las entradas originales para construir el modelo completo de la red neuronal.

Posteriormente, se compila el modelo utilizando un optimizador Adam, función de pérdida de entropía cruzada binaria, y exactidud binaria como métrica.

Por último, se entrena el modelo compilado con los datos y etiquetas de entrenamiento, empleando 100 épocas para el entrenamiento. La exactitud final del modelo fue mayor al 80%.

### Evaluación del modelo

Después de entrenar el modelo, se evalúa con los datos y etiquetas de prueba. Se confirma una exactitud final mayor al 80% también.

### Diagnóstico interactivo

Para finalizar el proyecto, se implementó un cuestionario interactivo que permite al usuario ingresar los datos de un paciente hipotético para que la red neuronal produzca su predicción de si el paciente tiene o no una enfermedad cardíaca. El cuestionario tiene validación de datos de entrada, repitiendo cada pregunta y mostrando un breve mensaje de error hasta que el usuario ingrese un valor válido. Para que el usuario sepa qué valores son razonables para las preguntas médicas, antes de ejecutar el cuestionario se muestran los datos de dos pacientes aleatorios, uno con diagnóstico positivo y otro negativo.
