# Base de Datos PostgreSQL - Módulo de Citas y Tutorías Académicas

## Descripción General

Este módulo forma parte de una plataforma de administración y asistencia académica para una institución educativa.

Su objetivo es permitir que los alumnos y tutores puedan:

* Agendar sesiones de tutoría.
* Gestionar solicitudes de citas.
* Confirmar, cancelar o reprogramar sesiones.
* Dar seguimiento a las tutorías realizadas.
* Registrar observaciones y acuerdos generados durante cada sesión.

---

# Modelo de Base de Datos

## Tabla: alumnos

Almacena la información de los estudiantes que solicitan tutorías.

| Campo            | Tipo         | Restricciones             |
| ---------------- | ------------ | ------------------------- |
| id_alumno        | SERIAL       | PRIMARY KEY               |
| matricula        | VARCHAR(20)  | UNIQUE NOT NULL           |
| nombre           | VARCHAR(100) | NOT NULL                  |
| apellido_paterno | VARCHAR(100) | NOT NULL                  |
| apellido_materno | VARCHAR(100) |                           |
| correo           | VARCHAR(150) | UNIQUE NOT NULL           |
| carrera          | VARCHAR(100) | NOT NULL                  |
| semestre         | INTEGER      | NOT NULL                  |
| fecha_registro   | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP |

---

## Tabla: tutores

Almacena la información de los tutores académicos.

| Campo            | Tipo         | Restricciones             |
| ---------------- | ------------ | ------------------------- |
| id_tutor         | SERIAL       | PRIMARY KEY               |
| numero_empleado  | VARCHAR(20)  | UNIQUE NOT NULL           |
| nombre           | VARCHAR(100) | NOT NULL                  |
| apellido_paterno | VARCHAR(100) | NOT NULL                  |
| apellido_materno | VARCHAR(100) |                           |
| correo           | VARCHAR(150) | UNIQUE NOT NULL           |
| especialidad     | VARCHAR(150) |                           |
| fecha_registro   | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP |

---

## Tabla: disponibilidad_tutor

Permite registrar los horarios disponibles de cada tutor.

| Campo             | Tipo        | Restricciones                |
| ----------------- | ----------- | ---------------------------- |
| id_disponibilidad | SERIAL      | PRIMARY KEY                  |
| id_tutor          | INTEGER     | REFERENCES tutores(id_tutor) |
| dia_semana        | VARCHAR(15) | NOT NULL                     |
| hora_inicio       | TIME        | NOT NULL                     |
| hora_fin          | TIME        | NOT NULL                     |
| activo            | BOOLEAN     | DEFAULT TRUE                 |

---

## Tabla: citas

Representa las sesiones de tutoría programadas.

| Campo          | Tipo        | Restricciones                 |
| -------------- | ----------- | ----------------------------- |
| id_cita        | SERIAL      | PRIMARY KEY                   |
| id_alumno      | INTEGER     | REFERENCES alumnos(id_alumno) |
| id_tutor       | INTEGER     | REFERENCES tutores(id_tutor)  |
| fecha_cita     | DATE        | NOT NULL                      |
| hora_inicio    | TIME        | NOT NULL                      |
| hora_fin       | TIME        | NOT NULL                      |
| modalidad      | VARCHAR(20) | NOT NULL                      |
| motivo         | TEXT        | NOT NULL                      |
| estado         | VARCHAR(20) | NOT NULL                      |
| fecha_creacion | TIMESTAMP   | DEFAULT CURRENT_TIMESTAMP     |

### Valores posibles para estado

* pendiente
* confirmada
* cancelada
* reprogramada
* completada

### Valores posibles para modalidad

* presencial
* virtual

---

## Tabla: seguimiento_tutoria

Almacena la información generada durante o después de la sesión.

| Campo           | Tipo      | Restricciones             |
| --------------- | --------- | ------------------------- |
| id_seguimiento  | SERIAL    | PRIMARY KEY               |
| id_cita         | INTEGER   | REFERENCES citas(id_cita) |
| observaciones   | TEXT      | NOT NULL                  |
| acuerdos        | TEXT      |                           |
| recomendaciones | TEXT      |                           |
| fecha_registro  | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

---

## Tabla: asistencia

Permite registrar si la tutoría fue atendida.

| Campo          | Tipo      | Restricciones             |
| -------------- | --------- | ------------------------- |
| id_asistencia  | SERIAL    | PRIMARY KEY               |
| id_cita        | INTEGER   | REFERENCES citas(id_cita) |
| asistio_alumno | BOOLEAN   | DEFAULT FALSE             |
| asistio_tutor  | BOOLEAN   | DEFAULT FALSE             |
| comentario     | TEXT      |                           |
| fecha_registro | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

---

# Relaciones

## Alumno - Cita

* Un alumno puede tener muchas citas.
* Cada cita pertenece a un único alumno.

Relación: 1:N

---

## Tutor - Cita

* Un tutor puede atender muchas citas.
* Cada cita pertenece a un único tutor.

Relación: 1:N

---

## Tutor - Disponibilidad

* Un tutor puede registrar varios horarios disponibles.

Relación: 1:N

---

## Cita - Seguimiento

* Una cita puede generar un registro de seguimiento.
* El seguimiento documenta acuerdos y observaciones de la sesión.

Relación: 1:1

---

## Cita - Asistencia

* Cada cita puede tener un registro de asistencia.

Relación: 1:1

---

# Diagrama Conceptual

```text
ALUMNOS
   |
   | 1
   |
   | N
CITAS ---------------- TUTORES
   |                       |
   |                       |
   |                       N
   |                       |
   |                 DISPONIBILIDAD_TUTOR
   |
   | 1
   |
   | 1
SEGUIMIENTO_TUTORIA

   |
   | 1
   |
   | 1
ASISTENCIA
```

# Beneficios de la Estructura

* Permite programar tutorías de forma organizada.
* Evita conflictos de horario mediante la disponibilidad del tutor.
* Facilita el seguimiento académico de cada estudiante.
* Genera historial de sesiones realizadas.
* Permite evaluar asistencia y participación.
* Escalable para futuras funcionalidades como notificaciones, videollamadas o reportes estadísticos.

# Conclusión

La estructura propuesta cubre el ciclo completo de una tutoría académica: desde la disponibilidad del tutor y la programación de la cita, hasta el registro de asistencia y el seguimiento posterior. Esto proporciona una base sólida para el desarrollo del sistema de gestión de tutorías dentro de la plataforma institucional.
