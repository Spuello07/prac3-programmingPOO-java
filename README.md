# Práctica 3 – Programación Orientada a Objetos en Java

**Curso:** Lenguajes y Paradigmas de Programación  
**Integrantes:**  
- Santiago José Puello Berrocal  
- Juan Daniel Vivas Granada  
- Sebastian Ibarra Prada  

---

## 1. Objetivos

- Aplicar conceptos básicos de programación orientada a objetos en Java.  
- Diseñar e implementar una clase de regresión lineal múltiple usando solo arreglos y operaciones básicas (sin librerías externas).  
- Desarrollar programas de prueba (`main`) que usen la clase de regresión para entrenar, predecir y evaluar el modelo.  
- Presentar el desarrollo de forma clara en GitHub, incluyendo estructura del código, resultados de pruebas, dificultades y conclusiones.

---

## 2. Estructura del repositorio

La práctica se organiza de la siguiente manera:

```
src/
├── RegresionLinear.java
├── MainRegresion.java
├── MainRegresionNotas.java
└── MainRegresionHelados.java
```

- **`RegresionLinear.java`**  
  Clase principal que implementa el modelo de regresión lineal (múltiple o simple) con:  
  - **Atributos:** `weights`, `bias`, `minimos`, `maximos`  
  - **Métodos públicos:**  
    - `Fit(double[][] X, double[] Y)` — Entrena el modelo con el conjunto de entrenamiento.  
    - `Predict(double[][] X)` — Realiza predicciones sobre nuevos datos.  
    - `Score(double[][] X, double[] Y)` — Calcula el error del modelo (R²).  
    - `DataScaling(double[][] X, boolean guardarEscala)` — Escala los datos con min–max y guarda la escala del entrenamiento.  
    - `getWeights()`, `getBias()` — Getters del modelo.  
  - **Métodos auxiliares privados:**  
    - `transponer`, `aColumna`, `mult`, `invertir` — Operaciones de álgebra lineal para la ecuación normal.  

- **`MainRegresion.java`**  
  Plantilla general para probar el modelo:
  1. Define `X_train` y `y_train`.  
  2. Crea una instancia de `RegresionLinear`.  
  3. Escala los datos de entrenamiento.  
  4. Ajusta el modelo (`Fit`).  
  5. Imprime los pesos (`weights`) y el `bias`.  
  6. Define `X_test`, predice y muestra las salidas.  
  7. Calcula el `score` del modelo.  

- **`MainRegresionNotas.java`**  
  Prueba el modelo con un dataset de notas de estudiantes.  
  - `X_train`: 4 características (notas parciales, talleres, etc.)  
  - `y_train`: nota final.  
  - Flujo: escalar datos → entrenar → imprimir resultados → predecir → mostrar `score`.  

- **`MainRegresionHelados.java`**  
  Prueba el modelo en un escenario de regresión simple (ventas de helados).  
  - `X_train`: una sola variable de entrada.  
  - `y_train`: ventas observadas.  
  - Flujo: escalar datos → entrenar → imprimir → predecir → evaluar.  

> Nota: Toda la lógica del modelo está concentrada en `RegresionLinear.java`, manteniendo un enfoque claro y orientado a objetos.

---

## 3. Explicación del código principal

En este apartado se explica el modelo `RegresionLinear` y un programa de prueba principal.

### Clase `RegresionLinear`

Implementa un modelo de regresión lineal con los siguientes componentes:

#### Atributos

- `double[] weights` — Pesos del modelo (uno por cada variable).  
- `double bias` — Intercepto del modelo.  
- `double[] minimos`, `double[] maximos` — Valores mínimos y máximos para el escalado min–max.

#### Métodos auxiliares

| Método | Descripción |
|--------|--------------|
| `transponer(A)` | Devuelve la transpuesta de `A`. |
| `aColumna(y)` | Convierte un vector en matriz columna. |
| `mult(A, B)` | Multiplicación de matrices. |
| `invertir(A)` | Calcula la inversa con el método de Gauss–Jordan. |

Estos métodos permiten implementar la ecuación normal sin usar librerías externas.

#### Entrenamiento: `Fit(double[][] X, double[] Y)`

1. Agrega una columna de 1s a `X` para el `bias`.  
2. Calcula `XT = Xᵀ`, `XTX = XT * X`, y su inversa `inv = (XTX)⁻¹`.  
3. Calcula `XTY = XT * Y`.  
4. Obtiene `B = inv * XTY`, de donde:
   - `bias = B[0][0]`  
   - `weights[i] = B[i+1][0]`  

#### Predicción: `Predict(double[][] X)`

Suma ponderada de las características:
```
y_i = bias + Σ(weights_j * X_ij)
```

#### Métrica: `Score(double[][] X, double[] Y)`

Calcula el coeficiente de determinación (R²):

```
R² = 1 - (SS_res / SS_tot)
```

- `SS_res`: suma de cuadrados del error.  
- `SS_tot`: variación total de los datos.

#### Escalado: `DataScaling(double[][] X, boolean guardarEscala)`

- Si `guardarEscala == true` → guarda min y max.  
- Si `guardarEscala == false` → usa los ya guardados.  

Asegura que los datos de entrenamiento y prueba estén en la misma escala.

---

## 4. Programa principal `MainRegresion`

Ejemplo general del flujo:

```java
RegresionLinear mlr = new RegresionLinear();
double[][] X_train_scaled = mlr.DataScaling(X_train, true);
mlr.Fit(X_train_scaled, y_train);
System.out.println(Arrays.toString(mlr.getWeights()));
System.out.println("Bias: " + mlr.getBias());
```

Luego se define `X_test`, se escala, se predice y se evalúa con `Score()`.

---

## 5. Resultados de las pruebas

### Prueba con datos de notas (`MainRegresionNotas.java`)

- Tipo: Regresión múltiple.  
- Salida: Pesos, bias, predicciones y `score (R²)`.

### Prueba con datos de helados (`MainRegresionHelados.java`)

- Tipo: Regresión simple.  
- Salida: Peso único, bias, predicciones y `score (R²)`.

---

## 6. Problemas y soluciones

| Problema | Solución |
|-----------|-----------|
| Inversión de matrices e índices fuera de rango | Se crearon métodos auxiliares (`transponer`, `mult`, `invertir`) y se probaron individualmente. |
| Inestabilidad numérica de XᵀX | Se usó escalado min–max para normalizar los datos. |
| Escalado inconsistente entre entrenamiento y prueba | `DataScaling` guarda los rangos y los reutiliza. |

---

## 7. Conclusiones

1. La POO facilita la reutilización del modelo.  
   La clase `RegresionLinear` puede usarse con distintos datasets sin modificar su lógica.

2. Implementar regresión desde cero refuerza los fundamentos matemáticos.  
   Se comprende mejor la ecuación normal y el significado de los parámetros.

3. El escalado de datos mejora la estabilidad numérica.  
   Evita divisiones por cero y mejora la precisión del modelo.

4. Los programas de prueba (`main`) validan el diseño modular.  
   Permiten verificar fácilmente los resultados en distintos escenarios.
