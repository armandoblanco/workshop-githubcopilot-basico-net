# 🏦 Workshop: GitHub Copilot para Contoso Banco

## Desarrollo Asistido por IA con .NET 8

![GitHub Copilot](https://img.shields.io/badge/GitHub%20Copilot-Enabled-green)
![.NET](https://img.shields.io/badge/.NET-8.0%20LTS-purple)
![Minimal APIs](https://img.shields.io/badge/Minimal%20APIs-8.x-blue)
![Swagger](https://img.shields.io/badge/Swagger-Swashbuckle-orange)
![Duración](https://img.shields.io/badge/Duración-2%20horas-red)

---

## 📋 Tabla de Contenidos

- [Introducción](#-introducción)
- [Conceptos Clave de GitHub Copilot](#-conceptos-clave-de-github-copilot)
- [Pre-requisitos](#️-pre-requisitos)
- [Agenda del Workshop](#-agenda-del-workshop)
- [Ejercicio 1: API REST con Copilot](#-ejercicio-1-api-rest-con-copilot-35-40-min)
- [Ejercicio 2: Frontend e Integración](#-ejercicio-2-frontend-e-integración-25-30-min)
- [Ejercicio 3: Tests y Refactoring](#-ejercicio-3-tests-y-refactoring-20-25-min)
- [Referencia Rápida](#-referencia-rápida)
- [Recursos Adicionales](#-recursos-adicionales)

---

## 🎯 Introducción

Este workshop práctico de **2 horas** te guiará en el desarrollo de un **Sistema Bancario para Contoso Banco**, una institución financiera ficticia. Utilizarás **GitHub Copilot** como asistente de desarrollo para construir una aplicación completa con C# y .NET 8. Aprenderás a:

- ✅ Usar el autocompletado y sugerencias inline de Copilot
- ✅ Escribir prompts efectivos que guíen a Copilot con intención
- ✅ Crear una API REST con Minimal APIs y documentación Swagger automática
- ✅ Desarrollar un frontend que consuma la API
- ✅ Generar pruebas unitarias asistidas por IA
- ✅ Usar Copilot Chat para explicar, corregir y refactorizar código

### Estándares del Proyecto

| Aspecto | Estándar |
|---------|----------|
| Tipo | API REST |
| Tecnología | .NET 8 (LTS) |
| Estilo de API | Minimal APIs |
| Documentación API | Swagger UI (Swashbuckle) |
| Idioma | Español (código, comentarios, documentación) |
| Base de datos | En memoria (listas y diccionarios C#) |
| Frontend | HTML + JavaScript vanilla + Bootstrap 5 (sin frameworks) |
| Pruebas | xUnit + WebApplicationFactory |

### Escenario: Contoso Banco 🏦

**Contoso Banco** es una institución financiera que necesita un sistema digital para gestionar:

- **Clientes**: datos personales y de contacto
- **Cuentas bancarias**: tipos de cuenta, saldos, estados
- **Transacciones** *(bonus)*: depósitos, retiros, transferencias

> 💡 La complejidad es **intencionalmente baja**. El objetivo NO es construir una app robusta, sino mostrar cómo **Copilot acelera cada fase del desarrollo**.

> 📝 **Sobre las transacciones:** Los ejercicios guiados cubren **Clientes** y **Cuentas** como funcionalidad principal. Las **Transacciones** se presentan como un desafío opcional al final del Ejercicio 1 para quienes avancen más rápido. No te preocupes si no llegas a completarlas — el valor del workshop está en el proceso, no en terminar todo.

### Arquitectura de la Solución

```
┌─────────────────────────────────────────────────────────────┐
│                     NAVEGADOR WEB                           │
│                                                             │
│  ┌─────────────────────┐    ┌───────────────────────────┐   │
│  │   Frontend HTML     │    │      Swagger UI            │   │
│  │                     │    │   (auto-generado por       │   │
│  │  Bootstrap 5 (CDN)  │    │    Swashbuckle)            │   │
│  │  JavaScript vanilla │    │                           │   │
│  │  fetch() → API      │    │   /swagger                │   │
│  │                     │    │                           │   │
│  │  /                  │    │                           │   │
│  └────────┬────────────┘    └─────────┬─────────────────┘   │
│           │                           │                     │
└───────────┼───────────────────────────┼─────────────────────┘
            │  HTTP (mismo origen)       │
            ▼                           ▼
┌─────────────────────────────────────────────────────────────┐
│              SERVIDOR .NET 8 (Kestrel)                      │
│              (Program.cs)                                   │
│                                                             │
│  ┌──────────────┐   ┌──────────────────────────────────┐    │
│  │ Static Files │   │        Minimal APIs               │    │
│  │              │   │                                  │    │
│  │ wwwroot/     │   │  /api/clientes    → CRUD         │    │
│  │ index.html   │   │  /api/cuentas     → CRUD         │    │
│  │              │   │  /api/transacciones → CRUD (*)    │    │
│  └──────────────┘   └──────────┬───────────────────────┘    │
│                                │                            │
│                                ▼                            │
│                  ┌──────────────────────────┐               │
│                  │   Modelos (C#)           │               │
│                  │                          │               │
│                  │  Models/Cliente.cs       │               │
│                  │  Models/Cuenta.cs        │               │
│                  │  Models/Transaccion.cs   │               │
│                  │  (*) = bonus/opcional    │               │
│                  │                          │               │
│                  │  Services/               │               │
│                  │  ClienteServicio.cs      │               │
│                  │  CuentaServicio.cs       │               │
│                  │                          │               │
│                  │  Datos en memoria        │               │
│                  │  (List<T> / Dictionary)  │               │
│                  └──────────────────────────┘               │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  dotnet test                                         │   │
│  │  ContosoBanco.Tests/ClienteTests.cs                  │   │
│  │  ContosoBanco.Tests/ApiIntegrationTests.cs           │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘

(*) Transacciones es un desafío opcional (Paso 1.7)
```

**Flujo de la aplicación:**
1. El usuario abre `http://localhost:5000/` → .NET sirve `index.html` desde `wwwroot/` como archivo estático
2. El JavaScript dentro del HTML usa `fetch('/api/...')` para consumir la API REST
3. Los Minimal API endpoints enrutan cada petición al servicio correspondiente (clientes, cuentas, transacciones)
4. Los servicios C# procesan la lógica y almacenan datos en colecciones en memoria
5. Swagger UI está disponible en `/swagger` para explorar y probar la API

---

## 🧠 Conceptos Clave de GitHub Copilot

### ¿Qué es GitHub Copilot?

GitHub Copilot es un **asistente de programación impulsado por IA** que se integra directamente en tu editor de código. Funciona como un **par de programación** que:

- Sugiere líneas completas o bloques de código mientras escribes
- Entiende el contexto de tu proyecto (nombres de archivos, comentarios, código existente)
- Aprende de tus patrones y se adapta a tu estilo

### El Arte del Prompting en Copilot

La clave para obtener buenos resultados con Copilot está en **cómo describes lo que necesitas**. Los comentarios descriptivos e intencionales guían mejor a Copilot que instrucciones rígidas paso a paso.

**❌ Prompt débil:**
```
Desarrolla mi app bancaria
```

> ¿Qué está mal? No hay contexto del negocio, ni tecnología, ni qué se espera como resultado. Copilot tiene que **adivinar** todo.

**✅ Prompt efectivo:**
```csharp
// Endpoint Minimal API para obtener el saldo actual de una cuenta bancaria de Contoso Banco
// Recibe el número de cuenta como parámetro de ruta
// Retorna el saldo disponible y la fecha de última actualización como JSON
// Incluye documentación OpenAPI con .WithTags() y .Produces<>()
```

> Observa cómo el segundo comentario le da a Copilot **contexto** (Contoso Banco), **intención** (obtener saldo), **detalles** (parámetros y retorno) y **tecnología** (Minimal APIs con OpenAPI). Cuanto más específico seas con la intención, mejores serán las sugerencias.

> 📚 **¿Quieres más ejemplos de buenas prácticas?** Consulta el repositorio [github/awesome-copilot](https://github.com/github/awesome-copilot) — contiene instrucciones, agentes y configuraciones contribuidas por la comunidad para sacar el máximo provecho de GitHub Copilot.

### ¿Qué es @workspace?

El `@workspace` es un participante de chat que proporciona contexto sobre todo tu espacio de trabajo (proyecto) a GitHub Copilot.

| Uso | Ejemplo |
|-----|---------|
| Buscar código | `@workspace ¿dónde se define la clase Cliente?` |
| Entender el proyecto | `@workspace ¿qué hace este proyecto?` |
| Encontrar patrones | `@workspace ¿cómo se implementan los endpoints?` |
| Generar código contextual | `@workspace crea un nuevo endpoint similar a los existentes` |

### Modos de GitHub Copilot Chat

> ⚠️ **Nota importante:** La interfaz de los modos (íconos, ubicación del selector, nombres) puede variar según tu versión de VS Code y la extensión de GitHub Copilot. Si ves una interfaz diferente a la descrita aquí, consulta con el instructor o revisa la [documentación oficial](https://docs.github.com/en/copilot).

GitHub Copilot tiene tres modos principales de operación:

#### 1️⃣ Modo Ask (Preguntar) 💬

| Aspecto | Detalle |
|---------|---------|
| Ícono | 💬 Burbuja de mensaje |
| Función | Solo responde preguntas, **NO** modifica archivos |
| Uso ideal | Explorar, entender, planificar, aprender |

**Ejemplo:**
```
[Modo Ask]
"¿Cuál es la mejor forma de implementar una API REST con Minimal APIs y Swagger?"

→ Copilot EXPLICA las opciones pero NO crea archivos
```

#### 2️⃣ Modo Agent (Agente) 🤖

| Aspecto | Detalle |
|---------|---------|
| Ícono | 🤖 Robot o chispa |
| Función | **PUEDE** crear y modificar archivos automáticamente |
| Uso ideal | Implementar cambios, crear código, refactorizar |

**Ejemplo:**
```
[Modo Agent]
"Crea una API REST con Minimal APIs para gestionar clientes de Contoso Banco"

→ Copilot CREA los archivos con todo el código
```

#### 3️⃣ Modo Plan (Planificar) 📋

| Aspecto | Detalle |
|---------|---------|
| Ícono | 📋 Lista o documento |
| Función | Genera un plan detallado **ANTES** de ejecutar |
| Uso ideal | Tareas complejas que involucran múltiples archivos |

**Ejemplo:**
```
[Modo Plan]
"Implementa la funcionalidad completa de transacciones bancarias 
con modelo, servicio y endpoints"

→ Copilot MUESTRA el plan:
  1. Crear Transaccion.cs (modelo)
  2. Crear TransaccionServicio.cs (lógica de negocio)
  3. Agregar endpoints de transacciones en Program.cs
  4. Registrar el servicio en el contenedor de DI

→ Tú APRUEBAS cada paso antes de que se ejecute
```

#### Comparativa de Modos

| Característica | Ask 💬 | Agent 🤖 | Plan 📋 |
|----------------|--------|----------|---------|
| Modifica archivos | ❌ No | ✅ Sí | ✅ Sí (con aprobación) |
| Velocidad | Rápido | Rápido | Más lento |
| Control | N/A | Bajo | Alto |
| Ideal para | Aprender | Implementar | Tareas complejas |
| Riesgo | Ninguno | Medio | Bajo |

### Comandos Especiales (/comando)

| Comando | Descripción | Ejemplo de uso |
|---------|-------------|----------------|
| `/tests` | Genera pruebas unitarias | Selecciona código → `/tests` |
| `/doc` | Genera documentación | Selecciona función → `/doc` |
| `/fix` | Propone corrección de errores | Selecciona código con error → `/fix` |
| `/explain` | Explica código seleccionado | Selecciona código → `/explain` |

---

## 🛠️ Pre-requisitos

### Software Necesario

```bash
# Verificar instalaciones
dotnet --version    # Debe ser 8.x (ejemplo: 8.0.400)
code --version      # Visual Studio Code
git --version       # Git
```

> 📝 **NOTA:** Este taller usa datos en memoria (colecciones de C#) para no requerir instalación de bases de datos. Los datos se pierden al reiniciar la aplicación, pero se cargan datos de ejemplo automáticamente al iniciar.

### Extensiones de VS Code

1. **GitHub Copilot** — Extensión principal
2. **GitHub Copilot Chat** — Chat integrado
3. **C# Dev Kit** (Microsoft) — Soporte para C# y .NET

### Cuenta de GitHub

- Necesitas una cuenta con acceso a **GitHub Copilot**
- Puede ser licencia individual, de organización o educativa

---

## 📅 Agenda del Workshop

| Hora | Bloque | Actividad | Modo Copilot |
|------|--------|-----------|--------------|
| 0:00 - 0:15 | Bienvenida | Setup, configuración e introducción | - |
| 0:15 - 0:55 | Ejercicio 1 | API REST con Minimal APIs + Swagger | Ask → Agent |
| 0:55 - 1:00 | ☕ Break | Descanso y Q&A rápido | - |
| 1:00 - 1:30 | Ejercicio 2 | Frontend HTML + integración con API | Agent |
| 1:30 - 1:50 | Ejercicio 3 | Pruebas unitarias y refactoring | Agent + /tests |
| 1:50 - 2:00 | Cierre | Recap, tips avanzados y recursos | - |

> ⏱️ Los tiempos son aproximados. Ajusta según el ritmo del grupo, pero **nunca excedas 2 horas**.

> 🎓 **Nota para el instructor:** Si la configuración inicial (Setup) toma más de 15 minutos por problemas de instalación, compensa reduciendo el Ejercicio 3: limítalo a solo generar tests con `/tests` (Pasos 3.1–3.3) y omite el refactoring y `/doc` (Pasos 3.4–3.5). Lo más importante es que los participantes completen los Ejercicios 1 y 2.

---

## 🔬 Ejercicio 1: API REST con Copilot (35-40 min)

### Objetivos

- ✅ Configurar instrucciones de Copilot para el proyecto
- ✅ Crear la estructura del proyecto .NET 8
- ✅ Implementar una API REST con Minimal APIs
- ✅ Obtener documentación Swagger automática con Swashbuckle
- ✅ Experimentar con el autocompletado de Copilot

### Paso 1.1: Explorar con Modo Ask 🔍

> 💡 **IMPORTANTE:** Asegúrate de estar en **Modo Ask** (ícono de mensaje 💬). Este modo NO modifica archivos, solo responde preguntas.

📍 **Cómo activar Modo Ask:**
1. Abre Copilot Chat (`Ctrl+Shift+I` / `Cmd+Shift+I`)
2. Busca el selector de modo en la parte superior
3. Selecciona **"Ask"** o el ícono de mensaje

🤖 **PROMPT — Copia y pega en Copilot Chat:**

```
Soy desarrollador en Contoso Banco y necesito diseñar un sistema bancario simple.

Ayúdame a entender:

1. ¿Qué entidades necesitaría para un sistema que gestione:
   - Clientes (nombre, email, teléfono, dirección)
   - Cuentas bancarias (tipo: ahorro/corriente, saldo, estado)
   - Transacciones (depósitos, retiros, transferencias)

2. ¿Qué endpoints REST serían necesarios para un CRUD básico?

3. ¿Cómo organizar esto usando .NET 8 con Minimal APIs?

4. ¿Cómo se integra Swagger automáticamente con Swashbuckle en Minimal APIs?
```

📝 **Observa:** Copilot responde con información detallada pero **NO crea ningún archivo**. Esto es ideal para la fase de exploración y planificación.

> 🌟 **Momento wow:** Observa cómo Copilot comprende el dominio bancario y sugiere una arquitectura coherente sin que le des detalles técnicos excesivos.

---

### Paso 1.2: Crear instrucciones de Copilot para el proyecto

> 💡 **¿Por qué ahora?** El archivo `copilot-instructions.md` configura a Copilot para que siga los estándares del proyecto en **todos** los archivos que genere de aquí en adelante. Crearlo antes de escribir código asegura que los modelos, la API y el frontend se generen con las convenciones correctas desde el inicio.

> 💡 **IMPORTANTE:** Cambia a **Modo Agent** (ícono de robot 🤖). Este modo **PUEDE** crear y modificar archivos.

📍 **Cómo activar Modo Agent:**
1. En Copilot Chat, busca el selector de modo
2. Selecciona **"Agent"** o el ícono de robot/chispa

🤖 **PROMPT en Modo Agent:**

```
Crea el archivo .github/copilot-instructions.md con instrucciones para que Copilot actúe como experto en C# y .NET para Contoso Banco:

# Instrucciones para GitHub Copilot - Proyecto Contoso Banco

## Idioma
- Todo el código, comentarios y documentación debe estar en **español**
- Mensajes de error en español
- Nombres de variables, propiedades y métodos en español (excepto palabras técnicas estándar como Get, Post, Api, Id)

## Estándares de Código
| Aspecto | Estándar |
|---------|----------|
| Tecnología | .NET 8 (LTS) |
| Estilo de API | Minimal APIs |
| Swagger | Swashbuckle |
| Estilo | Convenciones de C# de Microsoft |
| Documentación | Comentarios XML (///) en español |

## Nomenclatura
- Propiedades y métodos públicos: PascalCase en español (ObtenerClientes, CrearCuenta)
- Variables locales y parámetros: camelCase en español (clienteId, saldoActual)
- Clases y records: PascalCase (Cliente, CuentaBancaria, ClienteServicio)
- Constantes: PascalCase (SaldoMinimo, MaximoTransferencia)
- Interfaces: IPrefijo + PascalCase (IClienteServicio)

## Contexto del Proyecto
Este es un sistema bancario para Contoso Banco que gestiona clientes, cuentas y transacciones. Usa datos en memoria (colecciones C#) sin base de datos externa. Usa Minimal APIs (no Controllers). Usa records para DTOs cuando sea apropiado.
```

---

### Paso 1.3: Crear estructura del proyecto

🤖 **PROMPT en Modo Agent:**

```
Crea la estructura inicial del proyecto Contoso Banco con .NET 8.

Necesito:
- Un proyecto web con Minimal APIs usando "dotnet new web" llamado ContosoBanco
- Carpetas Models/ y Services/ dentro del proyecto
- Un proyecto de tests con xUnit llamado ContosoBanco.Tests usando "dotnet new xunit"
- Un archivo de solución (.sln) que agrupe ambos proyectos
- Agrega la referencia del proyecto principal al proyecto de tests

Ejecuta los comandos de dotnet CLI necesarios para crear todo.
```

📝 **Alternativa manual** (si el agente no ejecuta):
```bash
# Crear solución y proyectos
dotnet new sln -n ContosoBanco
dotnet new web -n ContosoBanco -o ContosoBanco
dotnet new xunit -n ContosoBanco.Tests -o ContosoBanco.Tests

# Agregar proyectos a la solución
dotnet sln add ContosoBanco/ContosoBanco.csproj
dotnet sln add ContosoBanco.Tests/ContosoBanco.Tests.csproj

# Referencia del proyecto de tests al principal
dotnet add ContosoBanco.Tests reference ContosoBanco

# Crear carpetas de organización
mkdir ContosoBanco/Models ContosoBanco/Services

# Agregar Swashbuckle al proyecto principal
dotnet add ContosoBanco package Swashbuckle.AspNetCore
```

> 📝 **Nota:** A diferencia de Python donde necesitas `__init__.py`, en .NET las carpetas son simplemente organización. Los namespaces se definen en cada archivo `.cs` y el compilador los resuelve automáticamente.

---

### Paso 1.4: Crear los modelos de datos con Copilot

Ahora vamos a ver cómo Copilot nos ayuda a escribir código a partir de **comentarios descriptivos**. En lugar de pedirle el código exacto, le daremos contexto e intención.

🤖 **PROMPT en Modo Agent:**

```
Crea el archivo ContosoBanco/Models/Cliente.cs con el modelo de datos para los clientes de Contoso Banco.

El modelo debe incluir:
- Una clase Cliente con propiedades: Id (int), Nombre (string), Email (string), Telefono (string), Direccion (string)
- Un record ClienteDto para crear/actualizar (sin Id, que se genera automáticamente)
- Una clase ClienteServicio en ContosoBanco/Services/ClienteServicio.cs que use una List<Cliente> en memoria como almacenamiento
- Datos de ejemplo precargados (3 clientes ficticios con nombres en español)
- Métodos para: ObtenerTodos, ObtenerPorId, Crear, Actualizar, Eliminar
- Cada método debe tener un comentario XML (///) en español que describa su propósito
- Registra el servicio como Singleton en el contenedor de DI (se usará en Program.cs)
```

📝 **¿La sugerencia es útil?** Observa el código generado:
- ¿Copilot usó nombres en español?
- ¿Los datos de ejemplo son coherentes con un banco?
- ¿Usó records para los DTOs?
- ¿Necesitas ajustar alguna propiedad?

> 💡 **Tip:** Si Copilot genera el código en inglés, prueba agregar al prompt: *"Recuerda seguir las instrucciones de .github/copilot-instructions.md"*. Esto refuerza las convenciones que configuraste en el Paso 1.2.

---

### Paso 1.5: Crear el modelo de cuentas bancarias

Ahora usaremos Copilot para generar el modelo de cuentas, siguiendo el mismo patrón que usamos con clientes.

🤖 **PROMPT en Modo Agent:**

```
Crea los archivos para las cuentas bancarias de Contoso Banco siguiendo el mismo patrón de Cliente:

1. ContosoBanco/Models/Cuenta.cs con:
   - Clase Cuenta con propiedades: Id (int), ClienteId (int), NumeroCuenta (string), TipoCuenta (string: "ahorro"/"corriente"), Saldo (decimal), Estado (string: "activa"/"inactiva"/"bloqueada"), FechaApertura (DateTime)
   - Un record CuentaDto para crear/actualizar (sin Id ni FechaApertura)

2. ContosoBanco/Services/CuentaServicio.cs con:
   - Misma estructura que ClienteServicio
   - Datos de ejemplo que coincidan con los clientes existentes (usa los mismos ClienteId)
   - Validación: el saldo no puede ser negativo (lanzar ArgumentException)
   - Usa decimal para montos (nunca double para dinero)

Sigue el mismo patrón y estilo que Cliente.cs y ClienteServicio.cs
```

📝 **Observa:**
- ¿Copilot detectó el patrón de `ClienteServicio.cs` y generó código consistente?
- ¿Los datos de ejemplo usan `ClienteId` que coinciden con los clientes existentes?
- ¿Incluyó la validación de saldo negativo que pediste?
- ¿Usó `decimal` para los montos en lugar de `double`?

> 🌟 **Momento wow:** Al mencionar "sigue el mismo patrón que ClienteServicio.cs", Copilot analiza el archivo existente y replica su estructura. ¡Cada persona puede obtener un resultado ligeramente diferente!

---

### Paso 1.6: Crear la aplicación con Minimal APIs y Swagger

Ahora le pediremos a Copilot que genere la aplicación principal. Observa cómo con un prompt conciso y orientado a la intención, Copilot puede generar una app completa.

🤖 **PROMPT en Modo Agent:**

```
Actualiza ContosoBanco/Program.cs como la aplicación principal de Contoso Banco.

Configura:
1. Swashbuckle para Swagger UI con título "API de Contoso Banco" y versión "v1"
2. Registra ClienteServicio y CuentaServicio como Singleton en el contenedor de DI
3. Habilita archivos estáticos (UseStaticFiles) para servir el frontend desde wwwroot/
4. Redirige "/" a "/index.html" con un MapGet simple
5. Endpoints Minimal API con grupos:
   - var clientes = app.MapGroup("/api/clientes").WithTags("Clientes")
     GET /           → obtener todos
     GET /{id}       → obtener por id (retorna 404 si no existe)
     POST /          → crear (retorna 201 con Location header)
     PUT /{id}       → actualizar (retorna 404 si no existe)
     DELETE /{id}    → eliminar (retorna 404 si no existe)
   - var cuentas = app.MapGroup("/api/cuentas").WithTags("Cuentas")
     Mismos endpoints CRUD
6. Usa .WithName(), .WithOpenApi() y .Produces<T>() para documentar cada endpoint
7. Agrega app.UseSwagger() y app.UseSwaggerUI()
```

> 💡 **Si la sugerencia no incluye algo que necesitas** (por ejemplo, falta la documentación Swagger o la ruta del frontend), prueba un prompt de seguimiento como: *"Agrega .WithOpenApi() y .Produces<>() a todos los endpoints para mejorar la documentación Swagger"* o *"Agrega UseStaticFiles y un redirect de / a /index.html"*. Iterar es parte natural de trabajar con Copilot.

---

### Paso 1.7: Ejecutar y explorar Swagger

🤖 **PROMPT en Modo Agent:**

```
Ejecuta la aplicación de Contoso Banco
```

📝 **Alternativa manual:**
```bash
cd ContosoBanco
dotnet run
```

**Abre en el navegador:** `http://localhost:5000/swagger`

> 📝 **Nota sobre el puerto:** Por defecto, `dotnet run` usa el puerto configurado en `Properties/launchSettings.json`. Si ves un puerto diferente (como 5176 o 5xxx), usa ese. Puedes forzar el puerto agregando `app.Urls.Add("http://localhost:5000");` en Program.cs o usando `dotnet run --urls "http://localhost:5000"`.

✅ **Verificar:**
- Swagger UI se muestra con el título "API de Contoso Banco"
- Los endpoints de clientes y cuentas aparecen organizados por tags
- Puedes probar los endpoints directamente desde Swagger (botón "Try it out")
- GET `/api/clientes` retorna los clientes de ejemplo

> 🌟 **Momento wow:** ¡Con Swashbuckle y `.WithOpenApi()`, Swagger UI se genera **automáticamente** a partir de los tipos de tus endpoints! Prueba hacer un POST desde Swagger para crear un nuevo cliente.

---

### Paso 1.8: Agregar endpoint de transacciones (⭐ desafío bonus)

> 📝 **Este paso es OPCIONAL.** Es un desafío para quienes terminaron los pasos anteriores antes de tiempo. Si el grupo va justo de tiempo, el instructor puede indicar que lo salten y pasen directamente al Ejercicio 2.

Este paso es un **mini-desafío**. Usa lo que aprendiste para crear la funcionalidad de transacciones con la ayuda de Copilot.

🤖 **PROMPT sugerido (adáptalo a tu estilo):**

```
@workspace Basándote en los patrones existentes del proyecto, crea la funcionalidad de transacciones bancarias:

1. Modelo en Models/Transaccion.cs con:
   - Propiedades: Id, CuentaOrigenId, CuentaDestinoId (nullable), Tipo (string: "deposito"/"retiro"/"transferencia"), Monto (decimal), Fecha (DateTime), Descripcion (string)
   - Un record TransaccionDto para crear (sin Id ni Fecha)
   - Validación: el monto debe ser positivo

2. Servicio en Services/TransaccionServicio.cs con datos de ejemplo

3. Registra el servicio y agrega endpoints en Program.cs con un grupo /api/transacciones y tag "Transacciones"

4. Incluye documentación OpenAPI con .WithOpenApi() en cada endpoint
```

> 💡 **Observa:** Al usar `@workspace`, Copilot analiza los archivos existentes y genera código que **sigue los mismos patrones** que ya usaste en clientes y cuentas.

---

### 🛠️ Troubleshooting Ejercicio 1

| Problema | Solución |
|----------|----------|
| `dotnet: command not found` | Instala el .NET 8 SDK desde https://dot.net |
| Error al compilar modelos | Verifica que los namespaces coincidan (`namespace ContosoBanco.Models`) |
| Swagger no aparece | Verifica que `app.UseSwagger()` y `app.UseSwaggerUI()` estén en Program.cs |
| Puerto en uso | Cambia con `dotnet run --urls "http://localhost:5001"` |
| Copilot genera Controllers en vez de Minimal APIs | Refuerza con "Usa Minimal APIs, NO Controllers. Sigue .github/copilot-instructions.md" |
| Error de inyección de dependencias | Verifica que los servicios estén registrados como Singleton antes de `var app = builder.Build()` |

---

## 🔬 Ejercicio 2: Frontend e Integración (25-30 min)

> ⚠️ **PRERREQUISITO:** Este ejercicio requiere que la API del Ejercicio 1 esté funcionando.

> 📝 **ENFOQUE SIMPLE:** El frontend es **una sola página HTML** (`wwwroot/index.html`) servida como archivo estático por .NET. Usa Bootstrap 5 vía CDN y JavaScript vanilla con `fetch()` para llamar a la API. **No se usa React, npm ni ningún framework frontend** — todo está en un solo archivo HTML.

### Objetivos

- ✅ Crear una página HTML servida como archivo estático que consuma la API
- ✅ Usar Copilot para generar código JavaScript vanilla con `fetch()`
- ✅ Integrar Bootstrap 5 vía CDN para una interfaz visual atractiva
- ✅ Practicar el uso de `/explain` para entender código generado

### Paso 2.1: Crear la página HTML del frontend

> 💡 **NOTA:** Todo el frontend vive en un solo archivo `wwwroot/index.html`. .NET lo sirve como archivo estático con `UseStaticFiles()` (ya configurado en Program.cs desde el Paso 1.6). El JavaScript vanilla dentro del HTML usa `fetch()` para comunicarse con la API.

🤖 **PROMPT en Modo Agent:**

```
Crea la carpeta wwwroot/ en el proyecto ContosoBanco y dentro el archivo wwwroot/index.html con la página principal del sistema de Contoso Banco.

Es una sola página HTML con JavaScript vanilla embebido (sin frameworks como React o Vue).

Requisitos:
1. Usar Bootstrap 5 via CDN (no instalar localmente, no usar npm)
2. Header con el nombre "Contoso Banco" y un ícono de banco (emoji o icono Bootstrap)
3. Barra de navegación con tabs: Inicio, Clientes, Cuentas
4. Sección de Inicio con:
   - Tarjetas (cards) mostrando estadísticas: Total Clientes, Total Cuentas
   - Las estadísticas se cargan dinámicamente desde la API con fetch()
5. Sección de Clientes con:
   - Tabla con los datos de clientes (se carga desde GET /api/clientes)
   - Botón "Nuevo Cliente" que muestra un modal con formulario
   - Botones de editar y eliminar en cada fila
6. Footer con "© Contoso Banco - Sistema de Gestión Bancaria"

Todo el JavaScript va dentro de una etiqueta <script> al final del HTML.
Usa colores profesionales bancarios (azul oscuro #1a237e, blanco, gris claro).
El JavaScript debe usar fetch() para consumir la API en /api/ (misma URL base).
Incluye manejo de errores con mensajes amigables al usuario.
```

> 📝 **Nota:** Observa que la barra de navegación solo incluye **Inicio, Clientes y Cuentas**. Si completaste el desafío bonus de transacciones (Paso 1.8), puedes agregar un tab de Transacciones más adelante.

---

### Paso 2.2: Agregar la sección de Cuentas al HTML

🤖 **PROMPT en Modo Agent:**

```
@workspace Actualiza wwwroot/index.html para agregar la sección de Cuentas bancarias.

Agrega dentro del mismo archivo HTML:
1. Tabla con las cuentas (número, tipo, saldo, estado, cliente asociado)
2. Los saldos deben mostrarse en formato de moneda ($ con separadores de miles)
3. Botón "Nueva Cuenta" con modal que incluya:
   - Selector de cliente (dropdown cargado desde la API con fetch)
   - Tipo de cuenta (ahorro/corriente)
   - Saldo inicial
4. Badge de color para el estado: activa (verde), inactiva (amarillo), bloqueada (rojo)
5. Funcionalidad de eliminar cuenta con confirmación

Usa el mismo patrón de JavaScript vanilla con fetch() que ya existe en la sección de Clientes.
```

> 💡 **Observa:** Al usar `@workspace` y mencionar "el patrón que ya existe", Copilot genera código **consistente** con lo que ya escribiste, todo dentro del mismo archivo HTML.

---

### Paso 2.3: Verificar que el frontend se sirve correctamente

> 📝 **Este paso puede ser innecesario** si en el Paso 1.6 Copilot ya configuró `UseStaticFiles()` y la redirección de `"/"` a `"/index.html"` en `Program.cs`. Verifica rápidamente:

🤖 **PROMPT en Modo Ask:**

```
@workspace ¿El archivo Program.cs ya tiene configurado UseStaticFiles() y una redirección de "/" a "/index.html"?
```

Si la respuesta es **sí**, pasa directo al Paso 2.4. Si la respuesta es **no**:

🤖 **PROMPT en Modo Agent:**

```
@workspace Actualiza Program.cs para:
1. Agregar app.UseStaticFiles() para servir archivos desde wwwroot/
2. Agregar un app.MapGet("/", () => Results.Redirect("/index.html")) para redirigir la raíz al frontend
No modifiques los endpoints de la API existentes.
```

---

### Paso 2.4: Ejecutar y probar la integración

🤖 **PROMPT en Modo Agent:**

```
Ejecuta la aplicación de Contoso Banco
```

📝 **Alternativa manual:**
```bash
cd ContosoBanco
dotnet run
```

**Abre en el navegador:**
- Frontend (página HTML): `http://localhost:5000/`
- Swagger UI (documentación API): `http://localhost:5000/swagger`

✅ **Verificar:**
- La página HTML de Contoso Banco carga correctamente en `/`
- Las estadísticas se muestran con datos reales de la API
- La tabla de clientes muestra los datos de ejemplo
- Se puede crear un nuevo cliente desde el formulario modal
- La sección de cuentas funciona con los datos de la API
- Swagger UI sigue accesible en `/swagger`

---

### Paso 2.5: Usar /explain para entender código (demostración)

> 💡 **CONCEPTO:** El comando `/explain` es perfecto para entender código que Copilot generó.

📍 **Instrucciones:**
1. Selecciona el bloque de JavaScript vanilla (dentro del `<script>` en el HTML) que hace `fetch()` a la API
2. Abre Copilot Chat y escribe:

```
/explain ¿Qué hace este código paso a paso? ¿Hay algún problema potencial?
```

📝 **Observa:** Copilot explica el flujo del código y puede señalar posibles mejoras como manejo de errores, timeouts o validaciones faltantes.

---

### 🛠️ Troubleshooting Ejercicio 2

| Problema | Solución |
|----------|----------|
| Página en blanco en `/` | Verifica que `Program.cs` tenga `app.UseStaticFiles()` y que `index.html` esté en `wwwroot/` |
| 404 en `/index.html` | Asegúrate de que la carpeta se llame exactamente `wwwroot` (no `Wwwroot` ni `wwwRoot`) |
| Fetch falla con "Network Error" | Confirma que la API esté corriendo en el puerto correcto |
| Bootstrap no carga | Verifica tu conexión a internet (se usa CDN) |
| Modal no se abre | Verifica que el JS de Bootstrap CDN esté incluido en el HTML |
| Datos no se muestran en la tabla | Abre la consola del navegador (F12) para ver errores |
| CORS error | No debería ocurrir (mismo origen), pero si usas puertos diferentes, agrega `builder.Services.AddCors()` |

---

## 🔬 Ejercicio 3: Tests y Refactoring (20-25 min)

> ⚠️ **PRERREQUISITO:** Este ejercicio requiere haber completado el Ejercicio 1 con la API funcionando.

> 🎓 **Nota para el instructor:** Si el tiempo es limitado, prioriza los Pasos 3.1–3.3 (generar y ejecutar tests). Los Pasos 3.4–3.5 (refactoring y documentación) son valiosos pero pueden omitirse sin afectar la experiencia central del workshop.

### Objetivos

- ✅ Generar pruebas unitarias automáticamente con `/tests`
- ✅ Usar Copilot para escribir tests de integración de la API
- ✅ Practicar refactoring asistido con `/fix`
- ✅ Generar documentación con `/doc`

### Paso 3.1: Generar pruebas unitarias con /tests

> 💡 **COMANDO ESPECIAL:** El comando `/tests` genera automáticamente pruebas unitarias para el código seleccionado.

📍 **Cómo usar /tests:**
1. Abre el archivo `ContosoBanco/Services/ClienteServicio.cs`
2. Selecciona todo el contenido del archivo (`Ctrl+A` / `Cmd+A`)
3. Abre Copilot Chat y escribe:

🤖 **PROMPT:**

```
/tests Genera pruebas unitarias completas con xUnit para este servicio.

Quiero pruebas que cubran:

1. Obtener todos los clientes (verifica que retorne la lista completa)
2. Obtener un cliente por Id válido e inválido
3. Crear un cliente con datos válidos
4. Crear un cliente con datos incompletos (falta Email, falta Nombre)
5. Actualizar un cliente existente
6. Actualizar un cliente que no existe
7. Eliminar un cliente existente
8. Eliminar un cliente que no existe

Requisitos:
- Usar xUnit con [Fact] y [Theory] donde aplique
- Nombres descriptivos en español con el patrón: MetodoAProbar_Escenario_ResultadoEsperado
- Patrón Arrange-Act-Assert con comentarios
- Cada test debe ser independiente (instanciar un nuevo servicio en cada test)
- Usar Assert.Equal, Assert.NotNull, Assert.Null, Assert.Throws según corresponda
```

> 🌟 **Momento wow:** Copilot genera un suite completo de tests incluyendo casos positivos y negativos, ¡a partir del código que tú escribiste!

---

### Paso 3.2: Crear archivo de pruebas

🤖 **PROMPT en Modo Agent:**

```
Guarda las pruebas generadas en ContosoBanco.Tests/ClienteServicioTests.cs

Asegúrate de que:
1. Los using/imports sean correctos para referenciar el proyecto ContosoBanco
2. Cada test crea su propia instancia de ClienteServicio para que sean independientes
3. El namespace sea ContosoBanco.Tests
```

> 📝 **Nota:** La referencia al proyecto principal ya se agregó en el Paso 1.3 con `dotnet add reference`, así que los imports deberían funcionar directamente.

---

### Paso 3.3: Generar pruebas de integración para la API

🤖 **PROMPT en Modo Agent:**

```
@workspace Crea pruebas de integración para la API de Contoso Banco en ContosoBanco.Tests/ApiIntegrationTests.cs

Usando WebApplicationFactory<Program> de Microsoft.AspNetCore.Mvc.Testing:

1. Agrega el paquete Microsoft.AspNetCore.Mvc.Testing al proyecto de tests
2. Asegúrate de que Program.cs sea accesible (agrega <InternalsVisibleTo Include="ContosoBanco.Tests" /> al .csproj del proyecto principal, o un partial class Program {} al final de Program.cs)
3. Crea una clase de tests que use WebApplicationFactory para levantar la API en memoria
4. Pruebas que verifiquen:
   - Que se puedan listar, consultar, crear, actualizar y eliminar clientes vía HTTP
   - Que los endpoints respondan con los códigos HTTP correctos (200, 201, 404, 400)
   - Que al consultar un recurso que no existe se obtenga 404
   - Que los endpoints de cuentas también respondan correctamente
5. Usa HttpClient con GetAsync, PostAsJsonAsync, PutAsJsonAsync, DeleteAsync
6. Deserializa las respuestas con response.Content.ReadFromJsonAsync<T>()

Cada test debe ser independiente y usar nombres descriptivos en español.
```

---

### Paso 3.4: Ejecutar las pruebas

🤖 **PROMPT en Modo Agent:**

```
Ejecuta todas las pruebas del proyecto Contoso Banco y muéstrame los resultados
```

📝 **Alternativa manual:**
```bash
dotnet test --verbosity normal
```

✅ **Verificar:**
- Todas las pruebas pasan (verde)
- Los tests unitarios y de integración se ejecutan correctamente
- No hay errores de compilación ni de referencia

---

### Paso 3.5: Refactoring con /fix y /explain *(si hay tiempo)*

> 💡 **CONCEPTO:** Ahora usaremos Copilot para **mejorar** el código existente.

📍 **Ejercicio de refactoring:**

1. Abre `ContosoBanco/Program.cs`
2. Selecciona un grupo de endpoints
3. Usa el siguiente prompt en Copilot Chat:

🤖 **PROMPT:**

```
/explain Analiza este código y dime:
1. ¿Hay código duplicado que se pueda simplificar?
2. ¿Faltan validaciones importantes?
3. ¿Hay algún problema de seguridad potencial?
4. ¿Cómo mejorarías el manejo de errores?
```

Después, si Copilot sugiere mejoras:

```
/fix Aplica las mejoras de seguridad y validación que acabas de sugerir
```

> 🌟 **Momento wow:** Copilot puede detectar **problemas de seguridad** como falta de validación de inputs, inyección potencial o manejo inadecuado de errores, y proponer correcciones específicas.

---

### Paso 3.6: Generar documentación con /doc *(si hay tiempo)*

📍 **Instrucciones:**
1. Abre `ContosoBanco/Services/CuentaServicio.cs`
2. Selecciona un método que no tenga comentarios XML
3. Usa el comando `/doc`:

🤖 **PROMPT:**

```
/doc Genera comentarios XML (///) completos en español para estos métodos.

Incluye:
- <summary> con descripción clara de qué hace cada método
- <param> para cada parámetro con tipo y descripción
- <returns> con el valor de retorno
- <exception> para excepciones que puede lanzar
- <example> con un ejemplo de uso
```

---

### 🛠️ Troubleshooting Ejercicio 3

| Problema | Solución |
|----------|----------|
| `CS0122: Program is inaccessible` | Agrega `public partial class Program { }` al final de `Program.cs` |
| Error de referencia al proyecto | Verifica con `dotnet list ContosoBanco.Tests reference` |
| Tests fallan por datos compartidos | Cada test debe crear su propia instancia del servicio |
| `dotnet test` no encuentra los tests | Ejecuta desde la raíz de la solución: `dotnet test ContosoBanco.Tests` |
| `/tests` genera código incompleto | Intenta seleccionar menos código o ser más específico en el prompt |
| `/fix` no aplica cambios | Cambia a Modo Agent para que Copilot pueda modificar archivos |
| Falta `Microsoft.AspNetCore.Mvc.Testing` | `dotnet add ContosoBanco.Tests package Microsoft.AspNetCore.Mvc.Testing` |

---

## 📖 Referencia Rápida

### Comandos de Copilot Chat

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `@workspace` | Contexto del proyecto entero | `@workspace ¿cómo se implementan los endpoints de clientes?` |
| `/tests` | Generar pruebas unitarias | Selecciona código → `/tests` |
| `/doc` | Generar documentación | Selecciona método → `/doc` |
| `/fix` | Proponer corrección | Selecciona código con error → `/fix` |
| `/explain` | Explicar código | Selecciona código → `/explain` |

### Atajos de Teclado

| Atajo | Acción |
|-------|--------|
| `Ctrl+I` / `Cmd+I` | Abrir Copilot inline en el editor |
| `Ctrl+Shift+I` / `Cmd+Shift+I` | Abrir panel de Copilot Chat |
| `Tab` | Aceptar sugerencia de Copilot |
| `Esc` | Rechazar sugerencia |
| `Alt+]` | Ver siguiente sugerencia |
| `Alt+[` | Ver sugerencia anterior |

### Modos de Copilot

| Modo | Cuándo usarlo |
|------|---------------|
| Ask 💬 | Explorar, entender, planificar (no modifica archivos) |
| Agent 🤖 | Implementar, crear archivos, hacer cambios |
| Plan 📋 | Tareas complejas multi-archivo (con aprobación) |

### Tips para Mejores Resultados con Copilot

| Tip | Ejemplo |
|-----|---------|
| **Nombres descriptivos** | `ObtenerSaldoCuenta()` en vez de `GetData()` |
| **Comentarios con intención** | `// Validar que el monto sea positivo antes de procesar` |
| **Contexto del negocio** | Mencionar "Contoso Banco", "cuenta bancaria", "transacción" |
| **Iterar sobre sugerencias** | Si la primera sugerencia no es perfecta, ajusta tu comentario |
| **Usar @workspace** | Para que Copilot entienda tu estructura de proyecto |
| **Referenciar archivos** | "Sigue el patrón de ClienteServicio.cs" para generar código consistente |

---

## 🆘 ¿Te quedaste atrás?

No te preocupes — el valor del workshop está en **experimentar con Copilot**, no en terminar todo el código.

| Situación | Qué hacer |
|-----------|-----------|
| No terminé el Ejercicio 1 | Pide al instructor la solución de referencia para poder continuar con el Ejercicio 2 |
| No terminé el Ejercicio 2 | El Ejercicio 3 solo necesita la API (Ejercicio 1). Puedes hacer tests sin frontend |
| No terminé el desafío de transacciones | ¡Es bonus! No afecta el resto del workshop |
| Copilot genera algo diferente a mi vecino | Eso es **normal y esperado**. Comparen resultados — es un buen ejercicio de aprendizaje |

> 🎓 **Para el instructor:** Se recomienda tener una rama `solucion` en el repositorio del workshop con el código completo de referencia. Esto permite que participantes que se queden atrás puedan alcanzar al grupo descargando los archivos necesarios.

---

## ✅ Checklist Final del Workshop

Al terminar el workshop, deberías tener:

### Configuración de Copilot
- [ ] `.github/copilot-instructions.md` — Instrucciones del proyecto

### Estructura de la Solución
- [ ] `ContosoBanco.sln` — Archivo de solución
- [ ] `ContosoBanco/ContosoBanco.csproj` — Proyecto web principal
- [ ] `ContosoBanco.Tests/ContosoBanco.Tests.csproj` — Proyecto de tests

### Proyecto Backend
- [ ] `ContosoBanco/Program.cs` — Aplicación con Minimal APIs, Swagger y archivos estáticos
- [ ] `ContosoBanco/Models/Cliente.cs` — Modelo y DTO de clientes
- [ ] `ContosoBanco/Models/Cuenta.cs` — Modelo y DTO de cuentas
- [ ] `ContosoBanco/Models/Transaccion.cs` — Modelo y DTO de transacciones *(⭐ bonus)*
- [ ] `ContosoBanco/Services/ClienteServicio.cs` — Lógica de negocio de clientes
- [ ] `ContosoBanco/Services/CuentaServicio.cs` — Lógica de negocio de cuentas
- [ ] `ContosoBanco/Services/TransaccionServicio.cs` — Lógica de transacciones *(⭐ bonus)*

### Frontend (archivo estático servido por .NET)
- [ ] `ContosoBanco/wwwroot/index.html` — Página HTML con Bootstrap 5 CDN y JavaScript vanilla
- [ ] Tabla de clientes funcional con CRUD (usando `fetch()`)
- [ ] Sección de cuentas con badges de estado
- [ ] Estadísticas dinámicas en la página de inicio

### Swagger
- [ ] Swagger UI accesible y funcional en `/swagger`
- [ ] Endpoints documentados con tags y tipos de respuesta
- [ ] Posibilidad de probar endpoints desde Swagger ("Try it out")

### Pruebas
- [ ] `ContosoBanco.Tests/ClienteServicioTests.cs` — Pruebas unitarias
- [ ] `ContosoBanco.Tests/ApiIntegrationTests.cs` — Pruebas de integración
- [ ] Todas las pruebas pasan con `dotnet test`

---

## 📚 Recursos Adicionales

### Documentación Oficial

- [GitHub Copilot Docs](https://docs.github.com/en/copilot)
- [VS Code + Copilot](https://code.visualstudio.com/docs/copilot/overview)
- [.NET 8 Documentation](https://learn.microsoft.com/dotnet/)
- [Minimal APIs Overview](https://learn.microsoft.com/aspnet/core/fundamentals/minimal-apis/overview)
- [Swashbuckle Documentation](https://learn.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger)
- [xUnit Documentation](https://xunit.net/docs/getting-started/netcore/cmdline)
- [Bootstrap 5](https://getbootstrap.com/docs/5.3/)

### Patrones y Buenas Prácticas

- [C# Coding Conventions](https://learn.microsoft.com/dotnet/csharp/fundamentals/coding-style/coding-conventions)
- [Minimal API Best Practices](https://learn.microsoft.com/aspnet/core/fundamentals/minimal-apis/min-api-filter)
- [Testing ASP.NET Core Apps](https://learn.microsoft.com/aspnet/core/test/integration-tests)

### Siguiente Nivel con Copilot

- **Copilot en la terminal:** Usa `Ctrl+I` en la terminal integrada de VS Code para generar comandos `dotnet`
- **Agentes personalizados:** Crea archivos `.github/agents/*.md` para especializar a Copilot
- **Copilot para Git:** Usa Copilot Chat para generar mensajes de commit descriptivos

---

## 🙋 Preguntas Frecuentes

### ¿Por qué usamos datos en memoria en lugar de Entity Framework?

| Ventaja | Detalle |
|---------|---------|
| Sin instalación | No requiere SQL Server, SQLite ni ningún motor de BD |
| Rápido | Operaciones instantáneas, ideal para un workshop |
| Portable | Funciona igual en cualquier máquina con .NET 8 |
| Simple | Permite enfocarnos en Copilot, no en configuración de BD |

**¿Cómo migrar a Entity Framework Core?**
1. Agregar los paquetes `Microsoft.EntityFrameworkCore` y `Microsoft.EntityFrameworkCore.Sqlite`
2. Crear un `DbContext` con `DbSet<Cliente>`, `DbSet<Cuenta>`, etc.
3. Reemplazar los servicios en memoria por repositorios con EF Core
4. Copilot puede ayudarte: *"@workspace migra los servicios en memoria a Entity Framework Core con SQLite"*

### ¿Por qué .NET 8 y no .NET 9?

.NET 8 es la versión **LTS** (Long Term Support) con soporte hasta noviembre de 2026. Además, .NET 9 removió Swashbuckle del template por defecto en favor de `Microsoft.AspNetCore.OpenApi`, lo cual agrega un paso de configuración adicional innecesario para un workshop. Si tu organización ya usa .NET 9, la migración es mínima: solo necesitas agregar Swashbuckle manualmente o usar el nuevo paquete de OpenAPI nativo.

### ¿Por qué Minimal APIs y no Controllers?

| Minimal APIs | Controllers |
|--------------|-------------|
| Menos ceremonia, código más conciso | Más estructura, más verboso |
| Todo en un archivo para empezar | Requiere carpeta Controllers/ + archivos separados |
| Ideal para APIs pequeñas/medianas | Ideal para APIs grandes con muchas convenciones |
| Patrón recomendado desde .NET 7+ | Patrón clásico, ampliamente conocido |

Para un workshop de 2 horas, Minimal APIs permite enfocarse en **Copilot** en vez de en la ceremonia de Controllers, herencia de `ControllerBase` y atributos.

### ¿Copilot genera código diferente para cada persona?

Sí, y eso es **intencional**. Copilot aprende del contexto (tu código, tus comentarios, tu estilo) y puede generar soluciones ligeramente diferentes para cada persona. Esto es una característica, no un error.

### ¿Cómo hago que Copilot genere código en español?

1. Configúralo en `.github/copilot-instructions.md` (Paso 1.2 — aplica para todo el proyecto)
2. Escribe comentarios y nombres de variables en español
3. Si aún genera en inglés, agrega *"Sigue las instrucciones de .github/copilot-instructions.md"* al prompt

### ¿Qué hago si Copilot sugiere código incorrecto?

1. **No aceptes ciegamente** — siempre revisa las sugerencias
2. Presiona `Esc` para rechazar y ajusta tu comentario/prompt
3. Usa `Alt+]` para ver sugerencias alternativas
4. Recuerda: Copilot es un **asistente**, no un reemplazo del desarrollador

### ¿Qué pasa si mi interfaz de Copilot se ve diferente?

La interfaz de GitHub Copilot (modos, íconos, selectores) se actualiza con frecuencia. Si los íconos o la ubicación de los modos no coinciden exactamente con lo descrito en este workshop, consulta la [documentación oficial de VS Code + Copilot](https://code.visualstudio.com/docs/copilot/overview) o pregunta al instructor.

---

## 👥 Créditos

**Workshop desarrollado para:** Demostración de GitHub Copilot

**Tecnologías:** GitHub Copilot, C# 12, .NET 8, Minimal APIs, Swashbuckle (Swagger), HTML + JavaScript vanilla, Bootstrap 5 (CDN), xUnit

**Duración:** 2 horas

**Escenario ficticio:** Contoso Banco (institución financiera ficticia para fines educativos)

---

> 🎉 **¡Gracias por participar!** Ahora tienes las herramientas para usar GitHub Copilot como tu par de programación en proyectos reales.
