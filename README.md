## 📝 Descripción General
El **Sistema Electoral Nacional** es una plataforma integral diseñada para gestionar procesos electorales de forma segura, transparente y eficiente. Permite administrar candidatos y electores, configurar elecciones, habilitar la votación en línea y verificar la integridad de los resultados, manteniendo en todo momento el secreto del voto.

## 🏗️ Arquitectura del Sistema
La solución se basa en una arquitectura de **microservicios** donde cada componente vive en su propio repositorio independiente de GitHub para garantizar seguridad, independencia y mantenibilidad.

### Componentes Principales
* **App Windows de Administración:** Gestión interna del proceso electoral, carga de electores, configuración de horarios y verificación de integridad de resultados.
* **App Web de Votación:** Interfaz para que los electores habilitados creen su contraseña y emitan su voto dentro del horario configurado.
* **Backend REST:** Microservicios en Java y Spring Boot que gestionan la lógica de negocio y exponen endpoints específicos.
* **Base de Datos:** SQL Server centralizado, con acceso exclusivo desde el backend; ninguna capa de presentación accede directamente a la base de datos.

## 🛠️ Stack Tecnológico

| Componente | Tecnología / Detalle |
| :--- | :--- |
| **Backend / API** | Java + Spring Boot + Maven |
| **Frontend Web** | Spring Boot + Thymeleaf (consume API REST) |
| **App Windows** | Windows Forms + .NET Framework (C#) |
| **Base de Datos** | SQL Server Express (Dev) -> SQL Server Full (Prod) |
| **Seguridad** | Spring Security + JWT + BCrypt |
| **Infraestructura** | Servidor local (Dev) -> AWS (Producción, por definir) |

## 📂 Ecosistema de Repositorios
Cada microservicio posee su propio ciclo de vida y repositorio independiente:

* **`auth-service`**: Validación de DNI, creación de contraseña, login y JWT.
* **`voting-service`**: Emisión de votos, validación de horario y control de doble voto.
* **`results-service`**: Consulta y agregación de resultados y estadísticas de participación.
* **`admin-service`**: Gestión de candidatos, electores, tipos de elección y configuración.
* **`integrity-service`**: Verificación de que votos en candidatos coincidan con electores que votaron.
* **`import-service`**: Carga masiva de electores desde CSV/Excel con validación de DNI.
* **`electoral-frontend`**: App Web Thymeleaf que consume los servicios anteriores.

## 🔒 Seguridad e Integridad
* **Secreto del Voto:** No existe relación entre el elector y el candidato votado en ninguna tabla; la tabla de electores solo registra si votó, no a quién.
* **Protección de Datos:** La columna `Cantidad_Votos` solo se modifica mediante el stored procedure `sp_registrar_voto`, el cual es el único punto de escritura autorizado.
* **Política de Contraseña:** Mínimo 6 caracteres, incluyendo al menos una mayúscula, una minúscula y un número.
* **Auditoría:** Toda operación sobre el conteo de votos queda registrada en una tabla de auditoría (`Auditoria_Votos`) de solo inserción.
* **Verificación de Integridad:** El sistema compara de forma atómica que la suma de votos en candidatos sea igual al conteo de electores que marcaron `Voto = 1`.
