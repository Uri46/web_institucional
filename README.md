# Sistema de Gestión Académica Web Institucional (SGA)

Plataforma web institucional orientada a la administración académica integral de instituciones de educación superior técnica.

El sistema centraliza y optimiza la gestión de planes de estudio, carreras técnicas oficiales, matriculación de estudiantes, asignación docente, comisiones, actas de cursada, emisión de analíticos y la generación del Libro Matriz reglamentario.

---

> [!NOTE]
> **Proyecto de Prácticas Profesionales:** Este sistema fue desarrollado desde cero durante las prácticas profesionales formativas. Todos los datos cargados en esta versión pública (estudiantes, calificaciones, docentes, teléfonos y domicilios) son **100% ficticios y generados algorítmicamente** con fines demostrativos. El catálogo de carreras y materias refleja planes de estudio curriculares públicos.

---

## 1. Problema que Resuelve y Destinatarios

### Problema
Históricamente, los institutos de educación superior técnica administran la información mediante planillas de cálculo dispersas y libros físicos manuscritos, lo que genera:
- Inconsistencias en los números de documento y CUIL entre legajos.
- Dificultades para consolidar el historial académico de los estudiantes al momento de emitir constancias.
- Tiempos prolongados en la confección manual del Libro Matriz anual exigido por la normativa educativa provincial.
- Falta de un canal de consulta unificado para estudiantes y docentes sobre sus estados de cursada y calificaciones.

### Destinatarios
- **Personal Directivo y Preceptoría:** Administración centralizada de legajos, carreras, planes de estudio, generación de Libro Matriz oficial y constancias analíticas.
- **Cuerpo Docente:** Visualización de sus comisiones asignadas, nóminas de alumnos y materias a cargo.
- **Estudiantes:** Consulta de historial curricular, porcentaje de asistencia, regularidad y materias cursadas.

---

## 2. Funcionalidades del Sistema por Rol

### Rol Administrativo / Directivo
- **Buscador Académico Unificado:** Localización ágil de estudiantes y docentes por DNI, CUIL, apellido o nombre, con visualización de ficha institucional completa y modal de edición rápida.
- **Carga Secuencial de Alumnos en 2 Pasos:**
  - *Paso 1:* Formulario validado con cálculo algorítmico del dígito verificador de CUIL (algoritmo oficial Módulo 11 de ANSES) y acumulación en matriz de sesión.
  - *Paso 2:* Confirmación por lotes e inserción atómica en base de datos.
- **Importación Masiva desde Excel:** Procesamiento de planillas `.xlsx` con normalización automática de caracteres, detección de duplicados y reporte detallado de filas procesadas y errores.
- **Generación del Libro Matriz Oficial:** Exportación dinámica en formato Excel normalizado respetando la disposición formal por folio, resolución y materias.
- **Emisión de Constancias Analíticas:** Generación de constancia de estado académico con cálculo dinámico de promedios en formato imprimible A4.
- **Gestión Curricular y de Carreras:** Alta, edición y visualización de tecnicaturas, materias correlativas, planes de estudio y comisiones por ciclo lectivo.
- **Gestión Docente:** Registro individual e importación masiva de docentes con titulación y asignación a comisiones.

### Rol Docente
- **Portal Docente:** Panel con las comisiones asignadas para el ciclo lectivo, turnos, horarios y materias a cargo.
- **Ficha Imprimible:** Consulta y descarga de la ficha de asignación docente institucional.

### Rol Alumno
- **Portal del Estudiante:** Visualización de las tecnicaturas en las que está inscripto, materias cursadas, porcentaje de asistencia acumulado, estado de aprobación (Promocionado, Final, Regular, Libre) y notas de parciales.

---

## 3. Stack Tecnológico y Arquitectura

### Stack Tecnológico
- **Lenguaje:** Python 3.12+
- **Framework Web:** Django (Arquitectura MTV desacoplada)
- **Base de Datos:** PostgreSQL en producción / SQLite local para desarrollo con `PRAGMA foreign_keys = ON` activado
- **Frontend:** Django Templates + Tailwind CSS + Vanilla JavaScript
- **Manejo de Hojas de Cálculo:** `openpyxl`
- **Archivos Estáticos:** WhiteNoise
- **Servidor WSGI:** Gunicorn
- **Contenedorización:** Docker & Docker Compose

### Arquitectura de Software (Patrón MTV / Clean Architecture)

El proyecto organiza sus responsabilidades desacoplando la lógica de negocio y la persistencia de los controladores web a través de un contenedor de dependencias (`config/contenedor.py`):

```mermaid
graph TD
    Client[Cliente / Navegador] -->|HTTP Request| Views[Capa de Controladores / Views]
    Views -->|Invocación| UseCases[Casos de Uso / Servicios de Aplicación]
    UseCases -->|Reglas de Negocio| Domain[Entidades de Dominio]
    UseCases -->|Persistencia| Repositories[Repositorios de Datos]
    Repositories -->|Django ORM| DB[(PostgreSQL / SQLite)]
    Views -->|Renderiza Contexto| Templates[Capa de Presentación / Templates HTML]
    Templates -->|HTTP Response| Client
```

---

## 📊 Ingeniería de Requerimientos y Modelo de Datos (Análisis de Sistemas)

Como parte del rol de **Analista de Sistemas**, el diseño del sistema contempló una especificación rigurosa de requerimientos y un modelado conceptual y lógico de base de datos relacional para garantizar consistencia operativa e integridad en la trazabilidad académica.

### 1. Requerimientos Funcionales (RF) Principales
- **RF-01 (Control de Acceso y Roles):** Autenticación institucional con segregación de privilegios para roles Directivo/Administrativo, Docente y Estudiante.
- **RF-02 (Búsqueda Académica Multicriterio):** Localización inmediata de legajos y docentes por DNI, CUIL, nombre o apellido, con acceso directo a la ficha académica consolidada.
- **RF-03 (Alta Secuencial de Alumnos):** Formulario de carga en dos fases con validación algorítmica del dígito verificador de CUIL (algoritmo Módulo 11 oficial de ANSES) y persistencia atómica en base de datos.
- **RF-04 (Importación Masiva desde Planillas):** Procesamiento e ingesta masiva de estudiantes y docentes a partir de planillas Excel (`.xlsx`), con saneamiento de caracteres y reporte estructurado de inconsistencias por fila.
- **RF-05 (Estructura Curricular Dinámica):** Modelado y administración de tecnicaturas superiores, resoluciones ministeriales, materias correlativas y apertura de comisiones por ciclo lectivo y turno.
- **RF-06 (Gestión de Cursada y Regularidad):** Registro de inscripciones a comisiones, seguimiento del porcentaje de asistencia y cómputo de la situación final (Promocionado, Final, Regular, Libre).
- **RF-07 (Generación del Libro Matriz y Analíticos):** Automatización del Libro Matriz reglamentario oficial agrupado por folios y exportación de certificados de analítico parcial con cálculo dinámico de promedios ponderados.

### 2. Requerimientos No Funcionales (RNF) Clave
- **RNF-01 (Seguridad Web Integral):** Protección contra ataques CSRF (Cross-Site Request Forgery) mediante tokens criptográficos por formulario, mitigación de XSS (Cross-Site Scripting) mediante sanitización y escape de plantillas, almacenamiento seguro de credenciales con hashing PBKDF2 con SHA-256 y sesiones con flags `HTTPOnly` y `SameSite`.
- **RNF-02 (Integridad Referencial y Consistencia ACID):** Restricciones de integridad referencial estrictas a nivel motor (`FOREIGN KEY` activas en SQLite mediante `PRAGMA foreign_keys = ON` y nativas en PostgreSQL), con transacciones atómicas (`transaction.atomic`) en operaciones batch de carga e importación para evitar estados inconsistentes.
- **RNF-03 (Generación de Reportes de Alto Rendimiento):** Exportación dinámica de planillas Excel normalizadas mediante la librería `openpyxl`, estructurando celdas, estilos y fórmulas oficiales de manera eficiente.
- **RNF-04 (Contenedorización y Portabilidad):** Arquitectura reproducible basada en Docker y Docker Compose para aislar el entorno de ejecución (Django + Gunicorn + PostgreSQL + WhiteNoise), facilitando el despliegue tanto en servidores productivos como en estaciones de desarrollo.
- **RNF-05 (Confiabilidad y Cobertura de Pruebas):** Suite de 75 pruebas unitarias y de integración automatizadas (`Django TestCase` / `unittest`) que validan la capa de servicios, repositorios, controladores y validadores algorítmicos.

### 3. Diagrama Entidad-Relación (DER)

A continuación se detalla el modelo de datos relacional normalizado que soporta la arquitectura del sistema:

```mermaid
erDiagram
    Persona ||--o| Alumno : "extiende (1:1)"
    Persona ||--o| Docente : "extiende (1:1)"
    Carrera ||--o{ PlanEstudio : "contiene"
    Materia ||--o{ PlanEstudio : "integra"
    PlanEstudio ||--o{ Comision : "abre"
    Comision ||--o{ ComisionDocente : "asigna"
    Docente ||--o{ ComisionDocente : "participa"
    Comision ||--o{ Cursada : "inscribe"
    Alumno ||--o{ Cursada : "cursa"
    Cursada ||--o{ Evaluacion : "registra"

    Persona {
        int id_persona PK
        string dni UK
        string cuil
        string nombre
        string apellido
        string localidad
        string telefono
        string mail
        date fecha_nacimiento
        string identidad
    }

    Alumno {
        int persona_id PK, FK
        string legajo
    }

    Docente {
        int persona_id PK, FK
        string titulo_mn
    }

    Carrera {
        string codigo_carrera PK
        string nombre_carrera
        string resolucion_vigente
    }

    Materia {
        string codigo_materia PK
        string nombre_materia
    }

    PlanEstudio {
        int id_plan PK
        string codigo_carrera FK
        string codigo_materia FK
        int anio_carrera
        int carga_horaria_anual
    }

    Comision {
        string codigo_comision PK
        int id_plan FK
        int anio_lectivo
        string turno
        string division
    }

    Cursada {
        int id_cursada PK
        string codigo_comision FK
        int id_alumno FK
        decimal porcentaje_asistencia
        string situacion_final
    }

    Evaluacion {
        int codigo_evaluacion PK
        int id_cursada FK
        string instancia
        decimal nota
        date fecha
    }
```

---

## 5. Puesta en Marcha Local (Paso a Paso)

### Requisitos Previos
- Python 3.12 o superior instalado.
- Git instalado.

### Opción A: Ejecución Nativa con SQLite

1. **Clonar el repositorio:**
   ```bash
   git clone https://github.com/Uri46/web_institucional.git
   cd web_institucional
   ```

2. **Crear y activar un entorno virtual:**
   ```bash
   # En Windows
   python -m venv venv
   .\venv\Scripts\activate

   # En Linux / macOS
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Instalar dependencias:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configurar el entorno:**
   Copiar la plantilla de variables de entorno:
   ```bash
   # En Windows (PowerShell / CMD)
   copy .env.example .env

   # En Linux / macOS
   cp .env.example .env
   ```
   > [!IMPORTANT]
   > **Avisos de Seguridad y Despliegue:**
   > - `DEBUG`: Viene configurado en `False` por defecto en la plantilla `.env.example`. El sistema sirve los archivos estáticos (CSS, JS, logos SVG) de forma optimizada y segura a través de WhiteNoise tanto en local como en producción.
   > - `SECRET_KEY`: En cualquier despliegue real o servidor público, genere una clave única, secreta y aleatoria.
   > - `ADMIN_PASSWORD`: Modifique la contraseña del usuario administrador (`admin1234`) por una clave robusta en cualquier entorno productivo.


5. **Aplicar migraciones:**
   ```bash
   python manage.py migrate
   ```

6. **Crear usuario administrador de prueba:**
   ```bash
   python manage.py crear_usuario_admin
   ```
   *(Crea el usuario `admin` con la contraseña configurada en `.env`, por defecto `admin1234`).*

7. **Cargar datos de demostración sintéticos:**
   ```bash
   python manage.py seed_data --alumnos 30 --docentes 10
   ```

8. **Recolectar archivos estáticos (requerido para WhiteNoise con DEBUG=False):**
   ```bash
   python manage.py collectstatic --noinput
   ```

9. **Iniciar el servidor de desarrollo:**
   ```bash
   python manage.py runserver
   ```
   Acceder desde el navegador a: `http://127.0.0.1:8000/login/`

---

### Opción B: Ejecución con Docker & Docker Compose

El proyecto incluye soporte para PostgreSQL contenerizado y arranque unificado.

1. Configurar `.env` a partir de `.env.example`:
   ```bash
   copy .env.example .env
   ```
   Asegurarse de definir `DB_PASSWORD=una_clave_segura` en el archivo `.env`.

2. Construir y levantar los contenedores:
   ```bash
   docker-compose up --build
   ```

3. Sembrar datos de prueba dentro del contenedor:
   ```bash
   docker-compose exec web python manage.py seed_data
   ```

4. Ingresar en el navegador a `http://localhost:8000`.

---

## 6. Usuarios de Prueba para la Demostración

| Rol | Usuario (Identificador) | Contraseña | Destino inicial |
|---|---|---|---|
| **Directivo / Admin** | `admin` | `admin1234` | Buscador General y Gestión |
| **Docente (Demo)** | `25000000` *(DNI del primer docente)* | `123456789` | Portal Docente |
| **Estudiante (Demo)** | `40000000` *(DNI del primer estudiante)* | `123456789` | Portal del Alumno |

*(Nota: Todos los docentes y alumnos generados por el comando `seed_data` pueden ingresar utilizando su número de DNI como usuario y `123456789` como clave inicial del portal).*

---

## 7. Pruebas Automatizadas

El proyecto cuenta con una suite completa de pruebas unitarias y de integración que validan el flujo de autenticación, control de acceso por roles, cálculo de CUIL, matriculación y generación de reportes:

```bash
python manage.py test tests
```
> Resultado verificado: **75 tests ejecutados exitosamente (OK)**.

---

## 8. Capturas de Pantalla

### Acceso al Sistema
![Inicio de Sesión](docs/capturas/01-login.png)
*Pantalla de inicio de sesión institucional con selector de tema visual (claro/oscuro).*

### Buscador de Estudiantes y Estado Académico
![Buscador de Alumnos y Docentes](docs/capturas/02-buscador.png)
*Buscador de estudiantes con filtros por carrera, año de cursada, género, nacionalidad y localidad.*

### Carga de Alumnos en Dos Pasos
![Carga de Alumnos en Dos Pasos](docs/capturas/03-carga-alumno.png)
*Formulario de alta de alumnos en dos pasos (ingreso de datos y confirmación en matriz).*

### Catálogo de Carreras y Planes de Estudio
![Catálogo de Carreras y Planes](docs/capturas/04-catalogo-planes.png)
*Catálogo institucional de carreras técnicas, resoluciones ministeriales y planes de estudio.*

### Constancia de Estado Académico Imprimible
![Constancia Analítica Imprimible](docs/capturas/05-analitico.png)
*Constancia de estado académico con datos de filiación, materias cursadas y espacios para firmas institucionales.*

### Portal del Estudiante
![Portal de Autogestión del Estudiante](docs/capturas/06-portal-estudiante.png)
*Portal del estudiante con resumen de rendimiento académico, asistencias y opción de impresión de analítico.*

---

## 9. Equipo de Desarrollo

Proyecto desarrollado en equipo colaborativo (7 integrantes) durante las prácticas profesionales de la tecnicatura, abarcando desde el relevamiento de requerimientos y diseño de arquitectura modular hasta la implementación, normalización relacional y suite de testing automatizado.

- **Rol:** Analista de Sistemas y Desarrollador Web Backend
