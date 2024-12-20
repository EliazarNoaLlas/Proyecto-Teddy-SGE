<h3 align="center">
  <img src="https://teedy.io/img/github-title.png" alt="Teedy" width=500 />
</h3>

[![Licencia: GPL v2](https://img.shields.io/badge/License-GPL%20v2-blue.svg)](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html)
[![Maven CI/CD](https://github.com/sismics/docs/actions/workflows/build-deploy.yml/badge.svg)](https://github.com/sismics/docs/actions/workflows/build-deploy.yml)

Teedy es un sistema de gestión documental de código abierto y liviano para individuos y empresas.

<hr />
<h2 align="center">
  ✨ <a href="https://github.com/users/jendib/sponsorship">¡Patrocina este proyecto si lo usas y lo aprecias!</a> ✨
</h2>
<hr />

![¡Nuevo!](https://teedy.io/img/laptop-demo.png?20180301)

# Demo

Una demostración está disponible en [demo.teedy.io](https://demo.teedy.io)

- El acceso como invitado está habilitado con permisos de lectura en todos los documentos
- Usuario "admin" con contraseña "admin"
- Usuario "demo" con contraseña "password"

# Características

- Interfaz de usuario responsive
- Reconocimiento óptico de caracteres
- Autenticación LDAP ![¡Nuevo!](https://www.sismics.com/public/img/new.png)
- Soporte para archivos de imagen, PDF, ODT, DOCX, PPTX
- Soporte para archivos de video
- Motor de búsqueda flexible con sugerencias y resaltado
- Búsqueda de texto completo en todos los archivos soportados
- Todos los metadatos de [Dublin Core](http://dublincore.org/)
- Metadatos personalizados definidos por el usuario ![¡Nuevo!](https://www.sismics.com/public/img/new.png)
- Sistema de flujo de trabajo ![¡Nuevo!](https://www.sismics.com/public/img/new.png)
- Cifrado AES de 256 bits de los archivos almacenados
- Versionado de archivos ![¡Nuevo!](https://www.sismics.com/public/img/new.png)
- Sistema de etiquetas con anidación
- Importación de documentos desde correo electrónico (formato EML)
- Escaneo automático de bandeja de entrada e importación
- Sistema de permisos de usuario/grupo
- Autenticación de dos factores
- Grupos jerárquicos
- Registro de auditoría
- Comentarios
- Cuota de almacenamiento por usuario
- Compartir documentos por URL
- API Web RESTful
- Webhooks para disparar servicios externos
- Cliente Android completamente funcional
- [Importador masivo de archivos](https://github.com/sismics/docs/tree/master/docs-importer) (modo único o escáner)
- Probado con un millón de documentos

# Instalación con Docker

Hay disponible una imagen Docker preconfigurada, que incluye herramientas OCR y de conversión de medios, escuchando en el puerto 8080. Si no se proporciona configuración de PostgreSQL, la base de datos es una base de datos H2 embebida. La base de datos H2 embebida solo debe usarse para pruebas. Para uso en producción, utilice la configuración proporcionada de PostgreSQL (revise el ejemplo de Docker Compose).

**La contraseña de administrador predeterminada es "admin". No olvide cambiarla antes de pasar a producción.**

- Rama master, puede ser inestable. No recomendada para uso en producción: `sismics/docs:latest`
- Última versión estable: `sismics/docs:v1.11`

El directorio de datos es `/data`. No olvide montar un volumen en él.

Para construir URLs externos, el servidor espera una variable de entorno `DOCS_BASE_URL` (por ejemplo https://teedy.miempresa.com)

## Variables de entorno disponibles

- General
  - `DOCS_BASE_URL`: La URL base utilizada por la aplicación. Las URLs generadas usarán esta como base.
  - `DOCS_GLOBAL_QUOTA`: Define la cuota predeterminada que se aplica a todos los usuarios.
  - `DOCS_BCRYPT_WORK`: Define el factor de trabajo utilizado para el hash de contraseñas. El valor predeterminado es `10`. Este valor puede ser `4...31` incluyendo `4` y `31`. El valor especificado se usará para todos los usuarios nuevos y usuarios que cambien su contraseña. Tenga en cuenta que establecer este factor muy alto puede impactar severamente el rendimiento del inicio de sesión y la creación de usuarios.

- Administrador
  - `DOCS_ADMIN_EMAIL_INIT`: Define la dirección de correo electrónico que debería tener el usuario administrador al inicializar.
  - `DOCS_ADMIN_PASSWORD_INIT`: Define la contraseña que debería tener el usuario administrador al inicializar. Debe ser un hash bcrypt. **Tenga en cuenta que los `$` dentro del hash deben escaparse con un segundo `$`.**

- Base de datos
  - `DATABASE_URL`: La cadena de conexión jdbc que utilizará `hibernate`.
  - `DATABASE_USER`: El usuario que debe utilizarse para la conexión a la base de datos.
  - `DATABASE_PASSWORD`: La contraseña que debe utilizarse para la conexión a la base de datos.
  - `DATABASE_POOL_SIZE`: El tamaño del pool que debe utilizarse para la conexión a la base de datos.

- Idioma
  - `DOCS_DEFAULT_LANGUAGE`: El idioma que se utilizará por defecto. Los valores actualmente soportados son:
    - `eng`, `fra`, `ita`, `deu`, `spa`, `por`, `pol`, `rus`, `ukr`, `ara`, `hin`, `chi_sim`, `chi_tra`, `jpn`, `tha`, `kor`, `nld`, `tur`, `heb`, `hun`, `fin`, `swe`, `lav`, `dan`

- Correo electrónico
  - `DOCS_SMTP_HOSTNAME`: Nombre del host del servidor SMTP que utilizará Teedy.
  - `DOCS_SMTP_PORT`: El puerto que debe utilizarse.
  - `DOCS_SMTP_USERNAME`: El nombre de usuario que debe utilizarse.
  - `DOCS_SMTP_PASSWORD`: La contraseña que debe utilizarse.

## Ejemplos

En los siguientes ejemplos, algunas contraseñas están expuestas en texto claro. Esto se hizo para mantener los ejemplos simples. Recomendamos encarecidamente usar variables con un archivo `.env` u otros medios para almacenar sus contraseñas de forma segura.

### Predeterminado, usando PostgreSQL

```yaml
version: '3'
services:
# Aplicación Teedy
  teedy-server:
    image: sismics/docs:v1.11
    restart: unless-stopped
    ports:
      # Mapear puerto interno al host
      - 8080:8080
    environment:
      # URL base a utilizar
      DOCS_BASE_URL: "https://docs.ejemplo.com"
      # Establecer el correo del administrador
      DOCS_ADMIN_EMAIL_INIT: "admin@ejemplo.com"
      # Establecer la contraseña del administrador (en este ejemplo: "superSegura")
      DOCS_ADMIN_PASSWORD_INIT: "$$2a$$05$$PcMNUbJvsk7QHFSfEIDaIOjk1VI9/E7IPjTKx.jkjPxkx2EOKSoPS"
      # Configurar la conexión a la base de datos. "teedy-db" es el hostname
      # y "teedy" es el nombre de la base de datos a la que se
      # conectará la aplicación.
      DATABASE_URL: "jdbc:postgresql://teedy-db:5432/teedy"
      DATABASE_USER: "teedy_db_user"
      DATABASE_PASSWORD: "teedy_db_password"
      DATABASE_POOL_SIZE: "10"
    volumes:
      - ./docs/data:/data
    networks:
      - docker-internal
      - internet
    depends_on:
      - teedy-db

# Base de datos para Teedy
  teedy-db:
    image: postgres:13.1-alpine
    restart: unless-stopped
    expose:
      - 5432
    environment:
      POSTGRES_USER: "teedy_db_user"
      POSTGRES_PASSWORD: "teedy_db_password"
      POSTGRES_DB: "teedy"
    volumes:
      - ./docs/db:/var/lib/postgresql/data
    networks:
      - docker-internal

networks:
  # Red sin acceso a internet. La base de datos no necesita
  # acceso a la red del host.
  docker-internal:
    driver: bridge
    internal: true
  internet:
    driver: bridge
```

### Usando la base de datos interna (solo para pruebas)

```yaml
version: '3'
services:
# Aplicación Teedy
  teedy-server:
    image: sismics/docs:v1.11
    restart: unless-stopped
    ports:
      # Mapear puerto interno al host
      - 8080:8080
    environment:
      # URL base a utilizar
      DOCS_BASE_URL: "https://docs.ejemplo.com"
      # Establecer el correo del administrador
      DOCS_ADMIN_EMAIL_INIT: "admin@ejemplo.com"
      # Establecer la contraseña del administrador (en este ejemplo: "superSegura")
      DOCS_ADMIN_PASSWORD_INIT: "$$2a$$05$$PcMNUbJvsk7QHFSfEIDaIOjk1VI9/E7IPjTKx.jkjPxkx2EOKSoPS"
    volumes:
      - ./docs/data:/data
```

# Instalación manual

## Requisitos

- Java 11
- Tesseract 4 para OCR
- ffmpeg para miniaturas de video
- mediainfo para extracción de metadatos de video
- Un servidor de aplicaciones web como [Jetty](http://eclipse.org/jetty/) o [Tomcat](http://tomcat.apache.org/)

## Descarga

La última versión está disponible para descargar aquí: <https://github.com/sismics/docs/releases> en formato WAR.
**La contraseña de administrador predeterminada es "admin". No olvide cambiarla antes de pasar a producción.**

## Cómo construir Teedy desde las fuentes

Prerrequisitos: JDK 11, Maven 3, NPM, Grunt, Tesseract 4

Teedy está organizado en varios módulos Maven:

- docs-core
- docs-web
- docs-web-common

Primero, clone el repositorio: `git clone git://github.com/sismics/docs.git`
o descargue las fuentes desde GitHub.

### Iniciar la construcción

Desde el directorio raíz:

```console
mvn clean -DskipTests install
```

### Ejecutar una versión independiente

Desde el directorio `docs-web`:

```console

mvn jetty:run
```

### Construir un .war para desplegar en su contenedor de servlets

Desde el directorio `docs-web`:

```console
mvn -Pprod -DskipTests clean install
```

Obtendrá su WAR desplegable en el directorio `docs-web/target`.

# Contribuir

Todas las contribuciones son más que bienvenidas. Las contribuciones pueden cerrar un issue, corregir un bug (reportado o no reportado), mejorar el código existente, agregar nuevas funcionalidades, y así sucesivamente.

La rama `master` es la rama predeterminada y base para el proyecto. Se utiliza para desarrollo y todos los Pull Requests deberían ir allí.

# Licencia

Teedy se publica bajo los términos de la licencia GPL. Consulte `COPYING` para más
información o vea <http://opensource.org/licenses/GPL-2.0>.
