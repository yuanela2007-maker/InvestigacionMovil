# Agenda de actividades — Apache Cordova

Aplicación móvil híbrida para registrar actividades universitarias, laborales y personales. Está construida con **Apache Cordova** usando solo HTML, CSS y JavaScript, y se ejecuta en Android (emulador o dispositivo) y en el navegador para pruebas.

Proyecto del curso **Desarrollo de Software IV** — Universidad de Costa Rica, Sede del Pacífico.

**Integrantes:** Ashly Jiménez Calvo · Fiorella Duque Araya · Valeria Ramírez Rodríguez

---

## Funcionalidades

- **Dos vistas con navegación:** «Mi agenda» (lista) y «Nueva actividad» (formulario). Se navega con el botón **+**, la flecha **←** y el botón o gesto **atrás** de Android.
- **Formulario con validación:** título, fecha, hora, categoría y descripción, con mensajes de error por campo y un aviso de confirmación al guardar.
- **Listado dinámico:** actividades ordenadas por fecha y hora, con filtros (Todas, Pendientes y Completadas), opción de marcar como completada, botón para eliminar con confirmación y contador.
- **Persistencia:** los datos se guardan en el teléfono con `localStorage` y siguen ahí al cerrar la app.

### Reglas de validación

| Campo | Regla |
|---|---|
| Título | Obligatorio, mínimo 3 caracteres (máximo 60) |
| Fecha | Obligatoria, no puede ser anterior a hoy |
| Hora | Obligatoria |
| Categoría | Obligatoria: Universidad, Trabajo, Personal u Otro |
| Descripción | Opcional, máximo 200 caracteres |

---

## Requisitos y dependencias

### Software necesario

| Herramienta | Versión usada | Para qué sirve |
|---|---|---|
| [Node.js](https://nodejs.org/) y npm | 26.13.0 (mínimo ≥ 20.17.0 o ≥ 22.9.0) | Ejecutar Cordova CLI e instalar dependencias |
| Cordova CLI | 13.x | Crear, preparar, compilar y ejecutar el proyecto |
| JDK | **17** (Eclipse Temurin 17.0.21) | Compilar la parte nativa de Android |
| Gradle | 8.14.3 (instalado en el sistema) | Genera el Gradle Wrapper del proyecto |
| Android Studio | Rabbit 2 (2026.2.2 Patch 1) | Instalar el SDK y crear el emulador |
| Android SDK | Platform API 36, Build-Tools 36.0.0, Command-line Tools (latest), Platform-Tools, Emulator | Compilar e instalar la app |
| Emulador | Pixel 8, Android 16 (API 36), x86_64 | Ejecutar la app |

> ⚠️ **No usar JDK 25 ni versiones mayores.** cordova-android 15 requiere JDK 17, y Gradle 8.14 solo funciona hasta Java 24.

### Dependencias del proyecto (`package.json`)

| Paquete | Versión | Descripción |
|---|---|---|
| `cordova-android` | 15.1.0 | Plataforma Android de Cordova |
| `cordova-browser` | (la instalada por `cordova platform add browser`) | Plataforma para probar en el navegador |

- **Plugins:** ninguno. La app solo usa APIs web estándar (`localStorage`, eventos del DOM).
- **Librerías de JavaScript externas:** ninguna. Es HTML, CSS y JavaScript sin frameworks.

Las versiones exactas instaladas se pueden revisar con `cordova -v` y `cordova platform ls`.

---

## Configuración del entorno (Windows)

1. Instalar Node.js, el **JDK 17** (marcar la opción *Set JAVA_HOME variable*) y Android Studio.
2. En Android Studio, abrir **More Actions → SDK Manager**:
   - En **SDK Platforms**, instalar **Android 16.0 (API 36)**.
   - En **SDK Tools**, marcar *Show Package Details* e instalar **Build-Tools 36.0.0**, **Command-line Tools (latest)**, **Platform-Tools** y **Android Emulator**.
3. En **More Actions → Virtual Device Manager**, crear un **Pixel 8** con imagen de API 36.
4. Descargar Gradle 8.14.3 (*binary-only*) y descomprimirlo en `C:\Gradle`.
5. Crear las variables de entorno:
   - `ANDROID_HOME` = `C:\Users\<usuario>\AppData\Local\Android\Sdk`
   - `JAVA_HOME` = carpeta del JDK 17 (por ejemplo, `C:\Program Files\Eclipse Adoptium\jdk-17.x.x-hotspot`)
6. Agregar al **Path**:
   ```
   %ANDROID_HOME%\platform-tools
   %ANDROID_HOME%\cmdline-tools\latest\bin
   %ANDROID_HOME%\emulator
   C:\Gradle\gradle-8.14.3\bin
   ```
7. Si el emulador indica *Windows Hypervisor Platform is not enabled*, activarla y reiniciar la computadora.
8. Instalar Cordova:
   ```
   npm install -g cordova
   ```

### Verificar el entorno

Abrir una terminal **nueva** de **cmd** (no PowerShell) y ejecutar:

```
node -v
java -version
gradle -v
adb --version
cordova -v
```

`java -version` debe indicar **17**, y ningún comando debe responder *"no se reconoce como un comando"*.

---

## Instalación y ejecución

> Usar **cmd**, no PowerShell. Se recomienda ubicar el proyecto **fuera de OneDrive**.

```
git clone <URL-del-repositorio>
cd <carpeta-del-repositorio>
npm install
cordova prepare
cordova requirements
```

| Comando | Qué hace |
|---|---|
| `npm install` | Descarga las dependencias listadas en `package.json` (crea `node_modules/`) |
| `cordova prepare` | Crea `platforms/android` y `platforms/browser` y copia `www/` dentro de ellas |
| `cordova requirements` | Revisa que JDK, Android SDK y Gradle estén bien instalados |

### Ejecutar en Android

Primero se enciende el emulador desde Android Studio (**Device Manager → ▶ Pixel 8**). Luego:

```
cordova run android
```

La primera compilación tarda varios minutos. Al final deben aparecer los mensajes `BUILD SUCCESSFUL`, `INSTALL SUCCESS` y `LAUNCH SUCCESS`, y la app se abre en el emulador.

### Ejecutar en el navegador (pruebas rápidas)

```
cordova run browser
```

La app se abre en Chrome en `http://localhost:8000`. Para detener el servidor se presiona `Ctrl + C`.

### Otros comandos útiles

| Comando | Uso |
|---|---|
| `cordova build android` | Compila y genera el `.apk` sin instalarlo |
| `cordova platform ls` | Muestra las plataformas instaladas y sus versiones |
| `cordova platform add android` | Agrega Android si no aparece en `platforms/` |

El instalable de depuración se genera en:
`platforms/android/app/build/outputs/apk/debug/app-debug.apk`

> Cada vez que se modifica algo en `www/`, hay que volver a ejecutar `cordova run android` para verlo en el emulador.

---

## Estructura del proyecto

```
agenda/
├── config.xml           Nombre (Agenda), id (com.grupo.agenda) y versión de la app
├── package.json         Plataformas y dependencias del proyecto
├── package-lock.json    Versiones exactas de las dependencias
├── .gitignore           Excluye node_modules/, platforms/ y plugins/
├── README.md
└── www/                 Código de la aplicación
    ├── index.html       Única página: barra superior y las dos vistas
    ├── css/
    │   └── index.css    Estilos y diseño adaptable (mobile first)
    └── js/
        └── index.js     Lógica: arranque, navegación, validación, lista y datos
```

Carpetas que se generan en cada computadora y **no se suben** al repositorio:

- `node_modules/`: se crea con `npm install`.
- `platforms/`: se crea con `cordova prepare` o `cordova platform add`. Contiene el proyecto Android nativo.

### Cómo está organizado `index.js`

| Sección | Contenido |
|---|---|
| 1. Estado | `actividades`, `filtroActual`, `vistaActual` y la clave `agenda.actividades` |
| 2. Arranque | `deviceready` → `iniciarApp()` (punto de entrada) y `conectarEventos()` |
| 3. Navegación | `mostrarVista()` y `manejarBotonAtras()` (evento `backbutton`) |
| 4. Formulario | `guardarActividad()`, `validar()`, `mostrarErrores()` y `limpiarError()` |
| 5. Lista | `renderizarLista()`, `crearElemento()`, `cambiarCompletada()` y `borrarActividad()` |
| 6. Almacenamiento | `cargarActividades()` y `guardarEnStorage()` con `localStorage` y JSON |
| 7. Utilidades | `fechaHoy()`, `formatearFecha()` y `mostrarAviso()` |

### Flujo de ejecución

1. Android abre la app y la WebView carga `index.html`, que incluye `cordova.js` y `js/index.js`.
2. Cordova dispara `deviceready` y se ejecuta `iniciarApp()`.
3. Se cargan las actividades guardadas, se conectan los eventos y se muestra la lista.
4. Al guardar, el formulario se valida, la actividad se agrega al arreglo, se guarda en `localStorage` y la lista se vuelve a dibujar.

---

## Datos de prueba para la demostración

Actividades sugeridas para cargar durante la demo. Las fechas deben ser de hoy en adelante.

| Título | Fecha | Hora | Categoría | Descripción |
|---|---|---|---|---|
| Exponer Apache Cordova | 19/11/2026 | 10:00 | Universidad | Presentación grupal del framework |
| Reunión de grupo | 20/11/2026 | 08:30 | Universidad | Repasar la demo |
| Entregar informe | 21/11/2026 | 17:00 | Trabajo | — |
| Ir al gimnasio | 22/11/2026 | 06:00 | Personal | — |

Para probar la validación: guardar con el formulario vacío, o escribir un título de 1 o 2 letras.

---

## Limitaciones conocidas

- Los datos se guardan **solo en el dispositivo** (`localStorage`): no se sincronizan entre teléfonos y se pierden si se desinstala la app.
- Los datos del navegador y los del emulador son **independientes**.
- No se pueden **editar** actividades ya guardadas; solo se marcan como completadas o se eliminan.
- No hay notificaciones ni recordatorios.
- Solo se probó en **Android**. Compilar para iOS requiere macOS y Xcode.
- La plataforma `browser` sirve solo para pruebas: ahí no existe el botón atrás de Android.
- En la consola del navegador aparece un **404 de `cordova_plugins.js`**. Es normal porque el proyecto no tiene plugins.

---

## Solución de problemas

| Problema | Solución |
|---|---|
| `Current working directory is not a Cordova-based project` | Ubicarse en la carpeta que contiene `config.xml` |
| `gradle no se reconoce como un comando` | Revisar que `C:\Gradle\gradle-8.14.3\bin` esté en el Path y abrir una terminal nueva |
| La compilación falla con un error de Java | Verificar que `java -version` muestre 17 y no 25 |
| Error con `%USERPROFILE%` | El comando se escribió en PowerShell; usar cmd |
| *Windows Hypervisor Platform is not enabled* | Activar la característica en Windows y reiniciar |
| Error *Ruta de acceso demasiado larga* al copiar el proyecto | No copiar `node_modules/` ni `platforms/`; regenerarlas con `npm install` y `cordova prepare` |
