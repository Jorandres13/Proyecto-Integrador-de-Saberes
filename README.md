# Proyecto-Integrador-de-Saberes
FUNDAMENTO DE BASE DE DATOS Y PROGRAMACION FUNCIONAL

- Tablas de datos (nombre de columna, tipo, propósito y observaciones) - README.md
  | Columna | Tipo | Propósito | Observaciones |
  |--------|------|-----------|---------------|
  | adult | Boolean | Indica si la película es para público adulto | Valores `true` o `false` |
  | belongs_to_collection | JSON | Identifica si la película pertenece a una saga o colección | Presenta valores nulos en películas independientes |
  | budget | Int | Representa el presupuesto utilizado para producir la película | Puede contener valores nulos o atípicos |
  | genres | JSON | Muestra el género o géneros asociados a cada película | Puede contener uno o varios géneros |
  | homepage | String | Enlace a la página oficial de la película | Muchas películas no tienen enlace |
  | id | Int | Identificador único de cada película | Clave primaria del dataset |
  | imdb_id | String | Identificador único de la película en IMDb | No siempre está presente |
  | original_language | String | Idioma original en el que fue producida la película | Generalmente representado por códigos ISO |
  | original_title | String | Título original de la película | Puede diferir del título comercial |
  | overview | String | Breve resumen de la trama de la película | Texto descriptivo |
  | popularity | Double | Mide el impacto y relevancia de la película | Basado en interacción del público y críticos |
  | poster_path | String | Ruta del póster de la película | No es una URL completa |
  | production_companies | JSON | Información de las compañías productoras | Incluye nombre e ID de cada compañía |
  | production_countries | JSON | País o países donde se produjo la película | Puede contener múltiples países |
  | release_date | Date | Fecha oficial de lanzamiento de la película | Requiere conversión a formato fecha |
  | revenue | Int | Ingresos obtenidos por la película | Puede contener valores extremos |
  | runtime | Int | Duración total de la película en minutos | Presenta valores nulos en algunos casos |
  | spoken_languages | JSON | Idiomas hablados durante la película | Puede contener más de un idioma |
  | status | String | Estado de producción o lanzamiento de la película | Ej.: Released, Post Production |
  | tagline | String | Frase promocional o eslogan de la película | Puede estar vacía |
  | title | String | Título comercial de la película | Puede repetirse entre diferentes películas |
  | video | Boolean | Indica si el registro corresponde a un video extra | `false` indica que es una película completa |
  | vote_average | Int | Promedio de calificación otorgada por los usuarios | Escala de 0 a 100 |
  | vote_count | Int | Cantidad total de votos recibidos | Útil para medir popularidad |
  | keywords | JSON | Palabras clave asociadas a la película | Ayuda en análisis de contenido |
  | cast | JSON | Actores que participaron en la película | Lista de nombres y roles |
  | crew | JSON | Equipo técnico de la película | Incluye directores, productores, etc. |
  | ratings | JSON | Calificaciones individuales de usuarios | Incluye puntuación y momento del voto |

- Lectura de columnas numéricas
  | Columna | Tipo | Descripción |
  |--------|------|-------------|
  | `budget` | Int | Presupuesto asignado para la producción de la película |
  | `revenue` | Int | Ingresos totales obtenidos por la película |
  | `runtime` | Int | Duración de la película en minutos |
  | `popularity` | Double | Nivel de popularidad e impacto de la película |
  | `vote_average` | Int | Promedio de calificación otorgada por los usuarios |
  | `vote_count` | Int | Número total de votos recibidos |
  
  
- Análisis de datos en columnas numéricas (estadísticas básicas)
  | Columna | Media | Mediana | Moda |
  | ---------------- | ------------- | ---------------- | ----------- |
  | budget       | 3,481,145.83  | 0                | 0           |
  | popularity   | 2.15          | 0.52             | 0           |
  | revenue      | 16,632,438.85 | 0                | 0           |
  | runtime      | 99.05         | 98               | 90          |
  | vote_average | 5.36          | 5.9              | 6           |
  | vote_count   | 201.84        | 4                | 0           |
  
- Análisis de datos en columnas tipo texto (algunas col. - distribución de frecuencia). OJO: no considerar columnas en formato JSON
  | Columna               | Valor           | Frecuencia |
  | --------------------- | --------------- | ---------- |
  | original_language | en              | 75         |
  |                       | fr              | 7         |
  |                       | es              | 2         |
  |                       | it              | 2          |
  |                       | de              | 1          |
  |                       | ja              | 1          |
  |                       | da              | 3          |
  |                       | ru              | 1          |
  |                       | wo              | 1          |
  |                       | zh              | 1          |
  |                       | pt              | 1          |
  |                       | pl              | 1          |
  |                       | cs              | 1          |
  |                       | en              | 75         |
  |                       | hi              | 2          |
  | status            | Released        | 94         |
  |                       | Post Production | 2          |
  | original_title    | Valores únicos  | No aplica  |
  | title             | Valores únicos  | No aplica  |
  | overview          | Texto libre     | No aplica  |
  | tagline           | Texto libre     | No aplica  |

- Limpieza de datos (columnas con valores nulos, valores atípicos, etc.)
  
```Scala
  case class Movie(...)
```

 **Explicacion**
 
 Representa una fila completa del dataset con sus 28 columnas originales

Características clave

• Usa tipos fuertes (Boolean, Int, Long, Double, String)

• Refleja exactamente la estructura del CSV

• Facilita validación, limpieza y análisis posterior

• Permite trabajar con datos tipados en lugar de Strings crudos

Esto es fundamental para evitar errores al procesar datos reales incompletos o sucios

### Objetivo principal LimpiadorPeliculasCompleto

• No carga todo el archivo en memoria

• Es eficiente para archivos grandes

• Es completamente funcional


###  Codigo Completo

```Scala
 import cats.effect.{IO, IOApp}
import fs2.*
import fs2.io.file.{Files, Path}
import fs2.text

object LimpiezaTotal extends IOApp.Simple:

  val originalPath = Path("D:/ClaseO25F26/src/main/resources/data/pi_movies_complete (2).csv")
  val psudoPath    = Path("D:/ClaseO25F26/src/main/resources/data/pi_movies_complete (13).csv")

  val delimitante = ";"

  // Limpieza general
  def limpiarJson(s: String): String =
    s.trim
      .stripPrefix("\"")
      .stripSuffix("\"")
      .replaceAll("'", "\"")
      .replaceAll("\\bNone\\b", "null")
      .replaceAll("\\bTrue\\b", "true")
      .replaceAll("\\bFalse\\b", "false")
      .replaceAll("""\\(?!["\\/bfnrtu])""", "\\\\\\\\")

  val run: IO[Unit] =
    Files[IO]
      .readAll(originalPath)
      .through(text.utf8.decode)
      .through(text.lines)
      .map { line =>
        val columnas = line.split(delimitante, -1)

        //  LIMPIAR TODAS LAS COLUMNAS
        val columnasLimpias = columnas.map(limpiarJson)

        // Volver a unir la fila
        columnasLimpias.mkString(delimitante)
      }
      .intersperse("\n")
      .through(text.utf8.encode)
      .through(Files[IO].writeAll(psudoPath))
      .compile
      .drain
```
```
Cats Effect (IO, IOApp)
```

Manejo seguro y controlado de efectos como lectura y escritura de archivos

```
FS2 (Functional Streams for Scala)
```

Procesamiento del archivo como stream, evitando cargar todo el archivo en memoria
```
fs2.text
```
Decodificación UTF-8 y manejo de líneas de texto

```scala
val originalPath = Path("D:/ClaseO25F26/src/main/resources/data/pi_movies_complete (2).csv")
val psudoPath    = Path("D:/ClaseO25F26/src/main/resources/data/pi_movies_complete (13).csv")
```
originalPath: archivo CSV de entrada

psudoPath: archivo CSV de salida con los datos limpios

```scala
val delimitante = ";"
```
El archivo CSV usa ; como separador de columnas
Este delimitador se utiliza tanto para dividir como para reconstruir cada fila

### Función limpiarJson

La función limpiarJson recibe un String y lo transforma para que sea más compatible con JSON estándar, corrigiendo inconsistencias comunes

```scala
def limpiarJson(s: String): String =
  s.trim
    .stripPrefix("\"")
    .stripSuffix("\"")
    .replaceAll("'", "\"")
    .replaceAll("\\bNone\\b", "null")
    .replaceAll("\\bTrue\\b", "true")
    .replaceAll("\\bFalse\\b", "false")
    .replaceAll("""\\(?!["\\/bfnrtu])""", "\\\\\\\\")

```
### Transformaciones realizadas

Eliminación de espacios

- trim

- Quita espacios al inicio y al final del texto.

- Eliminación de comillas externas


```scala

stripPrefix("\"")

stripSuffix("\"")

```
- Elimina comillas dobles si el texto completo está encerrado entre ellas
  
###   Normalización de comillas
- Convierte comillas simples en dobles para cumplir el estándar JSON

###  Conversión de valores estilo Python

None → null

True → true

False → false

### Corrección de backslashes inválidos

- Escapa correctamente \ cuando no forman parte de un escape JSON válido.

- Evita errores posteriores al parsear strings JSON.

  ### Flujo de procesamiento (FS2 Stream)

  ```scala
  
  val run: IO[Unit] =
  Files[IO]
    .readAll(originalPath)
    .through(text.utf8.decode)
    .through(text.lines)
    .map { line =>
      val columnas = line.split(delimitante, -1)
      val columnasLimpias = columnas.map(limpiarJson)
      columnasLimpias.mkString(delimitante)
    }
    .intersperse("\n")
    .through(text.utf8.encode)
    .through(Files[IO].writeAll(psudoPath))
    .compile
    .drain

  ```
 ## Lectura del archivo
- Se leen los bytes del archivo CSV de entrada.

## Decodificación UTF-8
- Los bytes se convierten en texto.

## Separación por líneas
- Cada línea representa una fila del CSV.

## Procesamiento de cada fila
- La fila se divide en columnas usando `;`.
- Se limpian todas las columnas con la función `limpiarJson`.
- La fila se reconstruye utilizando el mismo delimitador.

## Inserción de saltos de línea
- Se asegura el formato correcto del archivo final.

## Escritura del archivo
- El contenido limpio se guarda en el archivo de salida.

## Ejecución del stream
- `compile.drain` ejecuta el flujo completo sin retornar valores.



  **Resultado**
  <img width="1273" height="767" alt="image" src="https://github.com/user-attachments/assets/d66413fa-97be-4f42-9e63-2ba8342f9b41" />

<img width="1280" height="851" alt="image" src="https://github.com/user-attachments/assets/4d4a9e59-17e9-47fb-9007-e6e9fad90803" />


 ## Consultar sobre librería circe (trabajo json en scala) 

 Circe es una librería de **Scala** utilizada para el manejo de **JSON**, permitiendo convertir datos JSON en objetos Scala y viceversa. Está diseñada para ser 
 
 **funcional**, **segura en tipos** y fácil de integrar con proyectos basados en **cats** y **cats-effect**

Circe trabaja principalmente con **case class**, lo que facilita el mapeo entre estructuras JSON y modelos del dominio

### Con Circe puedes:

- Convertir JSON a case class (decode)

- Convertir case class a JSON (encode)

- Navegar JSON (cursor)

### Objetivo principal

El objetivo de Circe es facilitar:

- La serialización de objetos Scala a JSON
- La  deserialización de JSON a objetos Scala
- El manejo seguro de errores sin usar excepciones
- El trabajo con datos complejos y anidados


####  Usar cualquier JSON pequeño para aprender circe
  
- Usar en algunas columnas JSON para obtener datos
  
- Solución al manejo de la columna Crew
  
- Limpieza completa de datos


```Scala
import cats.effect.{IO, IOApp}
import fs2.*
import fs2.io.file.{Files, Path}
import fs2.text

import _root_.io.circe.*
import _root_.io.circe.parser.*

object LimpiezaOrdenada extends IOApp.Simple:

  val originalPath = Path("D:/ClaseO25F26/src/main/resources/data/pi_movies_complete (2).csv")
  val psudoPath = Path("D:/ClaseO25F26/src/main/resources/data/pi_movies_ratings (11).csv")

  // Columna donde está el JSON de crew
  val indice = 27
  val delimitante = ";"

  // -------------------------
  // LIMPIEZA DE TEXTO
  // -------------------------
  def limpiarJson(s: String): String =
    s.trim
      .stripPrefix("\"")
      .stripSuffix("\"")
      .replaceAll("'", "\"")
      .replaceAll("\\bNone\\b", "null")
      .replaceAll("\\bTrue\\b", "true")
      .replaceAll("\\bFalse\\b", "false")
      .replaceAll("""\\(?!["\\/bfnrtu])""", "\\\\\\\\")

  // -------------------------
  // PARSEO REAL CON CIRCE
  // -------------------------
  def parseCrewConCirce(s: String): List[Map[String, String]] =
    if (s.isEmpty || s == "null") List.empty
    else
      parse(s) match
        case Left(_) => List.empty
        case Right(json) =>
          json.asArray match
            case None => List.empty
            case Some(arr) =>
              arr.toList.map { obj =>
                obj.asObject match
                  case None => Map.empty[String, String]
                  case Some(o) =>
                    o.toMap.map { case (k, v) =>
                      val valor =
                        if (v.isNull) ""
                        else
                          v.asString
                            .orElse(v.asNumber.map(_.toString))
                            .orElse(v.asBoolean.map(_.toString))
                            .getOrElse(v.noSpaces)

                      k -> valor
                    }
              }

  // -------------------------
  // PROGRAMA PRINCIPAL
  // -------------------------
  val run: IO[Unit] =
    Files[IO]
      .readAll(originalPath)
      .through(text.utf8.decode)
      .through(text.lines)
      .filter(_.nonEmpty)
      .map { line =>
        val columnas = line.split(delimitante, -1)

        val valor =
          if (columnas.length > indice) columnas(indice)
          else ""

        val limpio = limpiarJson(valor)
        parseCrewConCirce(limpio)
      }
      .compile
      .toList
      .flatMap { filasPorPelicula =>

        //  Obtener todas las columnas posibles
        val headers = filasPorPelicula.flatten.flatMap(_.keys).distinct

        //  Convertir cada objeto crew en una fila
        val filas = filasPorPelicula.flatten.map { fila =>
          headers.map(h => fila.getOrElse(h, "")).mkString(";")
        }

        val contenido =
          (headers.mkString(";") +: filas).mkString("\n")

        //  Escribir archivo
        Stream
          .emit(contenido)
          .through(text.utf8.encode)
          .through(Files[IO].writeAll(psudoPath))
          .compile
          .drain
      }
```


# Limpieza y Procesamiento de Datos (Movies Crew) 

Este módulo realiza la extracción, limpieza y normalización de datos complejos almacenados en formato JSON dentro de archivos CSV. Está diseñado específicamente para procesar la columna de personal técnico (*crew*) en datasets de cinematografía.

##  Descripción General

El script `LimpiezaOrdenada` utiliza un enfoque de programación funcional para transformar datos "sucios" (formateados como diccionarios de Python o JSON mal formado) en un archivo CSV estructurado y listo para análisis de datos o carga en bases de datos relacionales.



## Stack Tecnológico

* **Lenguaje:** Scala 3
* **Efectos:** [Cats Effect] (IOApp)
* **Streaming:** [FS2] (Functional Streams for Scala)
* **JSON Parsing:** [Circe]
* **I/O:** FS2 Files para manejo eficiente de recursos.

##  Especificaciones del Proceso

| Parámetro | Valor / Descripción |
| :--- | :--- |
| **Columna Objetivo** | Índice `27` (Información de Crew) |
| **Delimitador** | `;` (Punto y coma) |
| **Normalización** | Conversión de sintaxis Python (`None`, `True`, `False`) a JSON estándar. |
| **Aplanamiento** | Generación dinámica de cabeceras basada en las claves únicas del JSON. |

---

##  Lógica de Componentes

### 1. Limpieza de Texto (`limpiarJson`)
Corrige inconsistencias comunes en datasets de origen Python/Kaggle:
* Reemplaza comillas simples por dobles.
* Normaliza booleanos y nulos.
* Escapa caracteres especiales para evitar errores de parseo.

### 2. Parsing de JSON (`parseCrewConCirce`)
Utiliza la librería **Circe** para transformar los strings limpios en estructuras `List[Map[String, String]]`. Maneja de forma segura los diferentes tipos de datos (números, strings, booleanos) convirtiéndolos a una representación textual uniforme.

### 3. Pipeline de Datos (Streaming)
El proceso utiliza `fs2.Stream` para leer el archivo línea por línea, lo cual permite manejar archivos de gran tamaño sin saturar la memoria de forma inmediata.




```scala
import cats.effect.{IO, IOApp}
import fs2.*
import fs2.io.file.{Files, Path}
import fs2.text

object LimpiezaTotal extends IOApp.Simple:

  val originalPath = Path("D:/ClaseO25F26/src/main/resources/data/pi_movies_complete (2).csv")
  val psudoPath    = Path("D:/ClaseO25F26/src/main/resources/data/pi_movies_complete (13).csv")

  val delimitante = ";"

  // Limpieza general
  def limpiarJson(s: String): String =
    s.trim
      .stripPrefix("\"")
      .stripSuffix("\"")
      .replaceAll("'", "\"")
      .replaceAll("\\bNone\\b", "null")
      .replaceAll("\\bTrue\\b", "true")
      .replaceAll("\\bFalse\\b", "false")
      .replaceAll("""\\(?!["\\/bfnrtu])""", "\\\\\\\\")

  val run: IO[Unit] =
    Files[IO]
      .readAll(originalPath)
      .through(text.utf8.decode)
      .through(text.lines)
      .map { line =>
        val columnas = line.split(delimitante, -1)

        //  LIMPIAR TODAS LAS COLUMNAS
        val columnasLimpias = columnas.map(limpiarJson)

        // Volver a unir la fila
        columnasLimpias.mkString(delimitante)
      }
      .intersperse("\n")
      .through(text.utf8.encode)
      .through(Files[IO].writeAll(psudoPath))
      .compile
      .drain


```




- Documentar proceso en archivo README

