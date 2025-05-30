# Servicio Técnico

## Introducción

Este proyecto está diseñado para gestionar un sistema de servicio técnico. Permite almacenar información sobre los técnicos, clientes, fichas de servicio y las imágenes asociadas a los dispositivos en reparación. El sistema está construido con Spring Boot y MySQL como base de datos. A continuación se proporciona una descripción detallada de la estructura de la base de datos, su configuración, y los endpoints disponibles para interactuar con el sistema.

## Índice

1. [Estructura de la Base de Datos](#estructura-de-la-base-de-datos)
    - [Tabla: Tecnico](#tabla-tecnico)
    - [Tabla: Cliente](#tabla-cliente)
    - [Tabla: Ficha](#tabla-ficha)
    - [Tabla: ImagenDispositivo](#tabla-imagendispositivo)
2. [Configuración de la Base de Datos](#configuracion-de-la-base-de-datos)
3. [Relaciones entre Tablas](#relaciones-entre-tablas)
4. [📌 Mapeo de Endpoints](#-mapeo-de-endpoints)
    - [🔧 Técnicos](#-técnicos)
    - [👤 Clientes](#-clientes)
    - [📄 Fichas](#-fichas)
    - [🖼️ Imágenes](#-imágenes)
    - [🔍 Búsquedas Especiales](#-búsquedas-especiales)


## Estructura de la Base de Datos
### Tabla: Tecnico
``` sql
CREATE TABLE Tecnico (
    apodo VARCHAR(50) PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    numero_telefono VARCHAR(20)
);
```
### Tabla: Cliente
``` sql
CREATE TABLE Cliente (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    numero_telefono VARCHAR(20),
    dni VARCHAR(20)
);
```
### Tabla: Ficha
``` sql
CREATE TABLE Ficha (
    id INT PRIMARY KEY AUTO_INCREMENT,
    fecha_entrada DATE NOT NULL,
    fecha_salida DATE,
    rotura_cliente TEXT,
    diagnostico_tecnico TEXT,
    presupuesto DECIMAL(10,2),
    tecnico_apodo VARCHAR(50),
    cliente_id INT NOT NULL,
    FOREIGN KEY (tecnico_apodo) REFERENCES Tecnico(apodo) 
        ON DELETE SET NULL 
        ON UPDATE CASCADE,
    FOREIGN KEY (cliente_id) REFERENCES Cliente(id) 
        ON DELETE CASCADE 
        ON UPDATE CASCADE
);
```
### Tabla: ImagenDispositivo
``` sql
CREATE TABLE ImagenDispositivo (
    id INT PRIMARY KEY AUTO_INCREMENT,
    ficha_id INT NOT NULL,
    foto LONGBLOB NOT NULL,
    FOREIGN KEY (ficha_id) REFERENCES Ficha(id) 
        ON DELETE CASCADE 
        ON UPDATE CASCADE
);
```
## Configuración de la Base de Datos
### Pasos de Instalación:
1. Crear una base de datos MySQL llamada `ServicioTecnico`
2. Ejecutar los scripts de creación de tablas en el orden mostrado
3. Configurar el archivo con las credenciales de tu base de datos `application.properties`

### Configuración de Spring Boot (application.properties):
``` properties
spring.datasource.url=jdbc:mysql://localhost:3306/servicioTecnico?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```
## Relaciones entre Tablas
### Ficha → Técnico
- Relación: Muchos a Uno
- La ficha puede existir sin técnico asignado
- Al eliminar un técnico, sus fichas mantienen el ID en NULL
- Al actualizar el apodo de un técnico, se actualiza en todas sus fichas

### Ficha → Cliente
- Relación: Muchos a Uno
- La ficha requiere un cliente asignado
- Al eliminar un cliente, se eliminan todas sus fichas
- Al actualizar un cliente, se actualizan todas las referencias

### ImagenDispositivo → Ficha
- Relación: Muchos a Uno
- Cada imagen pertenece a una única ficha
- Al eliminar una ficha, se eliminan todas sus imágenes
- Al actualizar una ficha, se mantienen las referencias de las imágenes


## 📌 Mapeo de Endpoints

### 🔧 Técnicos

| Método | Endpoint                           | Función                          |
|--------|------------------------------------|----------------------------------|
| GET    | `/tecnico`                         | Obtener todos los técnicos       |
| GET    | `/tecnico/{apodo}`                 | Buscar técnico por apodo         |
| GET    | `/tecnico/telefono/{numero}`      | Buscar técnico por teléfono      |
| POST   | `/tecnico`                        | Crear nuevo técnico              |
| PUT    | `/tecnico/{apodo}`                | Actualizar técnico               |
| DELETE | `/tecnico/{apodo}`                | Eliminar técnico                 |

### 👤 Clientes

| Método | Endpoint                            | Función                          |
|--------|-------------------------------------|----------------------------------|
| GET    | `/cliente`                          | Obtener todos los clientes       |
| GET    | `/cliente/{id}`                    | Buscar cliente por ID            |
| POST   | `/cliente`                         | Crear nuevo cliente              |
| PUT    | `/cliente/{id}`                    | Actualizar cliente               |
| DELETE | `/cliente/{id}`                    | Eliminar cliente                 |

### 📄 Fichas

| Método | Endpoint                             | Función                          |
|--------|--------------------------------------|----------------------------------|
| GET    | `/ficha`                            | Obtener todas las fichas         |
| GET    | `/ficha/{id}`                       | Buscar ficha por ID              |
| POST   | `/ficha`                            | Crear nueva ficha                |
| PUT    | `/ficha/{id}`                       | Actualizar ficha                 |
| DELETE | `/ficha/{id}`                       | Eliminar ficha                   |

### 🖼️ Imágenes

| Método | Endpoint                             | Función                          |
|--------|--------------------------------------|----------------------------------|
| GET    | `/imagen/{fichaId}`                  | Obtener imágenes por ID de ficha |
| POST   | `/imagen/create`                     | Subir nueva imagen               |
| DELETE | `/imagen/{id}`                       | Eliminar imagen                  |

### 🔍 Búsquedas Especiales

| Método | Endpoint                             | Función                                      |
|--------|--------------------------------------|----------------------------------------------|
| GET    | `/ficha/tecnico/{apodo}`            | Buscar fichas por técnico                    |
| GET    | `/ficha/cliente/{id}`               | Buscar fichas por cliente                    |
| GET    | `/ficha/fecha/{fecha}`              | Buscar fichas por fecha de entrada exacta    |
| GET    | `/ficha/fecha/{dia1}/{dia2}`        | Buscar fichas entre dos fechas               |
