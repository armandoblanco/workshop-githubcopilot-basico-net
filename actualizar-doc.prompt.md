---
name: actualiza-doc
description: Actualiza el README.md del proyecto Contoso Banco reflejando el estado actual del código, endpoints, modelos y estructura de archivos.
---

Analiza el estado actual del proyecto Contoso Banco y actualiza (o crea si no existe) el archivo README.md en la raíz del repositorio.

Antes de escribir nada, lee estos archivos para obtener el estado real del proyecto:
- [Especificación](../../docs/spec.md)
- [Program.cs](../../ContosoBanco/Program.cs)
- Todos los archivos en [Models/](../../ContosoBanco/Models/)
- Todos los archivos en [Services/](../../ContosoBanco/Services/)

Si existe un directorio de tests, revisa también:
- [ContosoBanco.Tests/](../../ContosoBanco.Tests/)

Si existe el frontend, revisa:
- [wwwroot/index.html](../../ContosoBanco/wwwroot/index.html)

## Estructura del README

Genera el README con estas secciones en este orden:

### 1. Encabezado
Nombre del proyecto, descripción de una línea, badges de tecnología (.NET 8, Minimal APIs, Swagger).

### 2. Descripción
Párrafo breve explicando qué es el proyecto y para qué sirve. Sin lenguaje promocional ni emojis excesivos.

### 3. Stack tecnológico
Tabla con tecnología, versión y propósito. Extraer las versiones reales de los archivos .csproj.

### 4. Estructura del proyecto
Árbol de archivos real del proyecto. Solo incluir los archivos que existen, no inventar archivos futuros. Usar formato de texto plano con indentación, no Mermaid para esto.

### 5. Endpoints de la API
Tabla con método HTTP, ruta, descripción y códigos de respuesta. Extraer esta información directamente de Program.cs leyendo los MapGet, MapPost, MapPut, MapDelete registrados. No documentar endpoints que no existan en el código.

### 6. Modelos de datos
Para cada modelo en Models/, listar las propiedades con su tipo de dato y una descripción corta. Extraerlo del código real, no de la especificación.

### 7. Cómo ejecutar
Comandos exactos para clonar, restaurar dependencias y ejecutar. Incluir la URL de Swagger y del frontend si wwwroot/index.html existe.

### 8. Cómo ejecutar tests
Solo incluir esta sección si el directorio ContosoBanco.Tests/ existe y tiene archivos .cs. Comando dotnet test con la ruta correcta.

### 9.. Diagrama
Agrega un diagrama de la solucion en mermaid 

## Reglas

- Idioma: español para todo el contenido
- No incluir secciones de funcionalidad que no exista en el código actual
- No inventar archivos, endpoints ni modelos. Si no encuentras algo, no lo documentes
- Si una sección queda vacía porque la funcionalidad aún no se implementó, omite la sección completa
- Los ejemplos de request/response en la sección de endpoints deben corresponder a los DTOs reales del código
- No incluir roadmap, TODOs ni secciones de "próximos pasos"
- Usar formato Markdown limpio sin HTML embebido
