# 🏫 Base de Datos Universidad - Ejemplos de MySQL

Este proyecto contiene ejemplos prácticos en MySQL usando una base de datos llamada **Universidad**, diseñada para poner en práctica los siguientes elementos:

- Consultas con `JOIN`
- Procedimientos almacenados
- Funciones almacenadas

---

## 🗂 Estructura de la Base de Datos

```sql
DROP DATABASE IF EXISTS Universidad;
CREATE DATABASE IF NOT EXISTS Universidad;
USE Universidad;

CREATE TABLE Estudiante (
    id_estudiante INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100),
    edad INT,
    carrera VARCHAR(100)
);

CREATE TABLE Curso (
    id_curso INT PRIMARY KEY AUTO_INCREMENT,
    nombre_curso VARCHAR(100),
    creditos INT
);

CREATE TABLE Matricula (
    id_matricula INT PRIMARY KEY AUTO_INCREMENT,
    id_estudiante INT,
    id_curso INT,
    semestre VARCHAR(10),
    nota_final DECIMAL(4,2),
    FOREIGN KEY (id_estudiante) REFERENCES Estudiante(id_estudiante),
    FOREIGN KEY (id_curso) REFERENCES Curso(id_curso)
);
```

```sql
INSERT INTO Estudiante (nombre, edad, carrera) VALUES
('Laura Martínez', 21, 'Ingeniería de Sistemas'),
('Carlos Pérez', 22, 'Administración'),
('Ana Gómez', 20, 'Ingeniería de Sistemas'),
('Diego Rodríguez', 23, 'Derecho');

INSERT INTO Curso (nombre_curso, creditos) VALUES
('Bases de Datos', 4),
('Contabilidad', 3),
('Derecho Civil', 5),
('Programación I', 4);

INSERT INTO Matricula (id_estudiante, id_curso, semestre, nota_final) VALUES
(1, 1, '2024-1', 4.2),
(1, 4, '2024-1', 4.7),
(2, 2, '2024-1', 3.8),
(3, 1, '2024-2', 4.0),
(4, 3, '2024-2', 4.5);

```

🔍 Ejemplo de Consulta JOIN

### Mostrar el nombre del estudiante, la carrera, el curso que matriculó, el semestre y la nota final:

```sql
SELECT
    E.nombre AS nombre_estudiante,
    E.carrera,
    C.nombre_curso AS Curso_matriculado,
    M.semestre,
    M.nota_final
FROM Estudiante E
JOIN Matricula M ON E.id_estudiante = M.id_estudiante
JOIN Curso C ON M.id_curso = C.id_curso;

```


## ⚙️ Procedimiento Almacenado

### 🎯 Registrar una matrícula

```sql
DELIMITER $$

CREATE PROCEDURE registrar_matricula(
    IN ps_id_estudiente INT,
    IN ps_id_curso INT,
    IN ps_semestre VARCHAR(20),
    IN ps_nota_final DECIMAL(4,2)
)
BEGIN
    INSERT INTO Matricula(id_estudiante, id_curso, semestre, nota_final)
    VALUES (ps_id_estudiente, ps_id_curso, ps_semestre, ps_nota_final);
END $$

DELIMITER ;

-- 📞 Llamada de prueba
CALL registrar_matricula(4, 2, '2025-1', 5.0);

```


## 📐 Función Almacenada

### 🎯 Calcular el promedio de notas de un estudiante

```sql
DELIMITER $$

CREATE FUNCTION fn_promedio_estudiantes(
    ps_id_estudiante INT
)
RETURNS DECIMAL(4,2)
DETERMINISTIC
BEGIN
    DECLARE promedio DECIMAL(4,2);

    SELECT AVG(nota_final)
    INTO promedio
    FROM Matricula
    WHERE id_estudiante = ps_id_estudiante;

    RETURN promedio;
END $$

DELIMITER ;

-- 📊 Consulta del promedio
SELECT fn_promedio_estudiantes(1) AS promedio;

```


## TRIGGER

### 🧩 Enunciado:

> Crea un `TRIGGER` que registre automáticamente una matrícula con **nota final en 0.0** si un estudiante se inscribe a un curso **pero no se especifica la nota** (es decir, `nota_final IS NULL` al insertar).
>
> ```sql
> DELIMITER $$
>
> CREATE TRIGGER trg_default_nota_final
> BEFORE INSERT ON Matricula
> FOR EACH ROW
> BEGIN
>     IF NEW.nota_final IS NULL THEN
>         SET NEW.nota_final = 0.0;
>     END IF;
> END$$
>
> DELIMITER ;
>
> ```
