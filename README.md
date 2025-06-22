

# 🐬 MySQL II — 


## Consultas Avanzadas

---

## 🧱 1. Creación de Tablas

```sql
-- Tabla de clientes
CREATE TABLE cliente (
    id_cliente INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100)
);

-- Tabla de pedidos
CREATE TABLE pedido (
    id_pedido INT PRIMARY KEY AUTO_INCREMENT,
    cliente_id INT,
    total DECIMAL(10,2),
    FOREIGN KEY (cliente_id) REFERENCES cliente(id_cliente)
);

-- Tabla de proveedores
CREATE TABLE proveedor (
    id_proveedor INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(100)
);
```


## 📥 2. Insertar Datos de Ejemplo

```sql
-- Clientes
INSERT INTO cliente (nombre) VALUES
('Ana'), ('Luis'), ('Marta'), ('Juan');

-- Proveedores
INSERT INTO proveedor (nombre) VALUES
('Proveedor A'), ('Proveedor B');

-- Pedidos
INSERT INTO pedido (cliente_id, total) VALUES
(1, 50000),   -- Ana
(1, 70000),   -- Ana
(2, 30000),   -- Luis
(2, 80000),   -- Luis
(4, 150000);  -- Juan
```


## 🔍 3. Consultas Avanzadas

### 3.1 🔗 JOINs

**INNER JOIN** – Clientes con pedidos:

```sql
SELECT c.nombre, p.total
FROM cliente c
INNER JOIN pedido p ON c.id_cliente = p.cliente_id;

```

**LEFT JOIN** – Todos los clientes (con o sin pedidos):

```sql
SELECT c.nombre, p.total
FROM cliente c
LEFT JOIN pedido p ON c.id_cliente = p.cliente_id;

```

### 3.2 📊 GROUP BY + Agregación + HAVING

**Total vendido por cliente:**

```sql
SELECT c.nombre, SUM(p.total) AS total_vendido
FROM cliente c
JOIN pedido p ON c.id_cliente = p.cliente_id
GROUP BY c.id_cliente;

```

**Clientes que han comprado más de $100,000:**

```sql
SELECT c.nombre, SUM(p.total) AS total_vendido
FROM cliente c
JOIN pedido p ON c.id_cliente = p.cliente_id
GROUP BY c.id_cliente
HAVING total_vendido > 100000;

```

### 3.3 🧠 Subconsultas

**Clientes con pedidos mayores a $50,000:**

```sql
SELECT nombre
FROM cliente
WHERE id_cliente IN (
    SELECT cliente_id
    FROM pedido
    WHERE total > 50000
);

```

Cantidad de pedidos por cliente (subconsulta en SELECT):

```sql
SELECT
    nombre,
    (SELECT COUNT(*) FROM pedido WHERE cliente_id = cliente.id_cliente) AS total_pedidos
FROM cliente;

```


### 3.5 🔀 UNION y UNION ALL

**Lista de todos los nombres (clientes + proveedores, sin duplicados):**

```sql
SELECT nombre FROM cliente
UNION
SELECT nombre FROM proveedor;

```

**Mismo resultado pero con duplicados permitidos:**

```sql
SELECT nombre FROM cliente
UNION ALL
SELECT nombre FROM proveedor;

```


## ✅ Recomendaciones

* Usa `EXPLAIN` para analizar el rendimiento de tus consultas.
* Compara los resultados entre `INNER JOIN` y `LEFT JOIN`.
* Experimenta cambiando los datos de entrada para observar los cambios en los resultados.
* Practica el uso de `HAVING` con distintas condiciones y funciones agregadas.
