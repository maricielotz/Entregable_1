# Entregable_1
CREATE DATABASE BD_CLINICA
GO

USE BD_CLINICA
GO

###Tabla Empleados
CREATE TABLE Empleados (
  id_empleado INT PRIMARY KEY,
  nombre VARCHAR(50),
  apellido VARCHAR(50),
  fecha_contratacion DATE,
  id_departamento INT,
  salario DECIMAL(10, 2),
  CONSTRAINT fk_departamento FOREIGN KEY (id_departamento) REFERENCES 
Departamentos(id_departamento)
);

###Tabla Departamentos
CREATE TABLE Departamentos (
  id_departamento INT PRIMARY KEY,
  nombre_departamento VARCHAR(100)
);

###Tabla Especialidades
CREATE TABLE Especialidades (
  id_especialidad INT PRIMARY KEY,
  nombre_especialidad VARCHAR(100)
);

###Tabla Adicional 
CREATE TABLE Empleados_Especialidades (
  id_empleado INT,
  id_especialidad INT,
  PRIMARY KEY (id_empleado, id_especialidad),
  CONSTRAINT fk_empleado FOREIGN KEY (id_empleado) REFERENCES Empleados(id_empleado),
  CONSTRAINT fk_especialidad FOREIGN KEY (id_especialidad) REFERENCES Especialidades
(id_especialidad)
);

###EMPLEADOS
INSERT INTO Empleados (id_empleado, nombre, apellido, fecha_contratacion, id_departamento, 
salario) VALUES
(1, 'Juan', 'Perez', '2015-03-15', 1, 2500.00),
(2, 'María', 'Gomez', '2017-08-20', 2, 2800.00),
(3, 'Pedro', 'Lopez', '2016-05-10', 1, 3000.00),
(4, 'Ana', 'Martinez', '2014-02-28', 3, 3200.00);



-- DEPARTAMENTOS

INSERT INTO Departamentos (id_departamento, nombre_departamento) VALUES

(1, 'Medicina General'),

(2, 'Pediatría'),

(3, 'Cirugía'),

(4, 'Odontología');

 

 -- ESPECIALIDADES

 INSERT INTO Especialidades (id_especialidad, nombre_especialidad) VALUES

(1, 'Cardiología'),

(2, 'Dermatología'),

(3, 'Ginecología'),

(4, 'Neurología');



-- ADICIONAL

INSERT INTO Empleados_Especialidades (id_empleado, id_especialidad) VALUES

(1, 1),

(1, 2);





-- PROCEDIMIENTOS ALMACENADOS

--1.

CREATE PROCEDURE Obtener_Empleados @id_departamento INT

AS

BEGIN

SELECT *

FROM Empleados

WHERE YEAR(fecha_contratacion) < 2018 AND id_departamento = @id_departamento;

END;



SELECT * FROM Empleados



-- 2.

CREATE PROCEDURE Insertar_Departamento @nombre_departamento VARCHAR(100)

AS

BEGIN

  INSERT INTO Departamentos (nombre_departamento)

  VALUES (@nombre_departamento);

END;



-- 3. 

CREATE PROCEDURE Promedio_Edad_Departamento

AS

BEGIN

  SELECT d.nombre_departamento, AVG(DATEDIFF(YEAR, e.fecha_contratacion, GETDATE())) 

AS promedio_edad

  FROM Departamentos d

  INNER JOIN Empleados e ON d.id_departamento = e.id_departamento

  GROUP BY d.nombre_departamento;

END;



-- 4. 

CREATE PROCEDURE Informacion_Empleado 

  @id_empleado INT

AS

BEGIN

  SELECT apellido, salario

  FROM Empleados

  WHERE id_empleado = @id_empleado;

END;





-- FUNCIONES

CREATE FUNCTION Calcular_Edad (@fecha_nacimiento DATE)

RETURNS INT

AS

BEGIN

  DECLARE @edad INT;

  SET @edad = DATEDIFF(YEAR, @fecha_nacimiento, GETDATE());

  RETURN @edad;

END;



-- CONSULTAS AVANZADAS

-- 1. ¿Cual es la cantidad de pacientes por especialidad?

SELECT es.nombre_especialidad, COUNT(*) AS cantidad_pacientes

FROM Empleados_Especialidades ee

JOIN Especialidades es ON ee.id_especialidad = es.id_especialidad

GROUP BY es.nombre_especialidad;



-- 2. ¿Cual es el salario promedio de cada empleado por departamento?

SELECT d.nombre_departamento, FORMAT(AVG(e.salario), 'C','es-PE') AS salario_promedio

FROM Empleados e

JOIN Departamentos d ON e.id_departamento = d.id_departamento

GROUP BY d.nombre_departamento;



-- REPORTES ESTADISTICOS

-- 1. Reporte de empleados por departamento

SELECT d.nombre_departamento, COUNT(*) AS cantidad_empleados

FROM Empleados e

JOIN Departamentos d ON e.id_departamento = d.id_departamento

GROUP BY d.nombre_departamento;



-- 2. Reporte de especialistas por especialidad

SELECT es.nombre_especialidad, COUNT(*) AS cantidad_especialistas

FROM Empleados_Especialidades ee

JOIN Especialidades es ON ee.id_especialidad = es.id_especialidad

GROUP BY es.nombre_especialidad;



-- 3. Reporte de empleados por antigüedad

SELECT YEAR(fecha_contratacion) AS año_contratacion, COUNT(*) AS cantidad_empleados

FROM Empleados

GROUP BY YEAR(fecha_contratacion)

ORDER BY YEAR(fecha_contratacion);
