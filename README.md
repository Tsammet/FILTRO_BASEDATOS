CARLOS AUGUSTO SÀNCHEZ DIAZ (BASE DE DATOS 2, Gourmet Delight)

CONSULTAS 

-- 1 Obtener la lista de todos los menús con sus precios

~~~mysql 
SELECT men.nombre, men.descripcion, men.precio 
FROM menus AS men;
~~~

-- 2 Encontrar todos los pedidos realizados por el cliente Juan Perez

~~~mysql
SELECT ped.id, ped.fecha, ped.total
FROM pedidos AS ped
INNER JOIN clientes AS cli
ON cli.id = ped.id_cliente
WHERE cli.nombre = 'Juan Perez';
~~~

-- 3 Listar los detalles de todos los pedidos, incluyendo el nombre del menù, cantidad y precio unitario

~~~mysql
SELECT ped.id, men.nombre, depe.cantidad, depe.precio_unitario
FROM pedidos AS ped
INNER JOIN detalles_pedidos AS depe
ON depe.id_pedido = ped.id
INNER JOIN menus AS men
ON men.id = depe.id_menu;
~~~

-- 4 calcular el total gastado por cada cliente en todos sus pedidos 

~~~mysql
SELECT cli.nombre, SUM(ped.total) AS 'TotalGastado'
FROM clientes AS cli
INNER JOIN pedidos AS ped
ON cli.id = ped.id_cliente
GROUP BY cli.nombre;
~~~

-- 5  Encontrar los menús con un precio mayor a $10

~~~mysql
SELECT men.id, men.nombre, men.precio
FROM menus AS men
WHERE men.precio > 10;
~~~

-- 6 Obtener el menú más caro pedido al menos una vez

~~~mysql
SELECT men.nombre, MAX(men.precio)
FROM menus AS men
INNER JOIN detalles_pedidos AS depe
ON depe.id_menu = men.id
GROUP BY men.nombre
ORDER BY MAX(men.precio) DESC
LIMIT 1;
~~~

-- 7 Listar los clientes que han realizado más de un pedido

~~~mysql
SELECT cli.nombre, cli.correo_electronico, COUNT(ped.id) AS 'cantidad pedidos'
from clientes AS cli
INNER JOIN pedidos AS ped
ON cli.id = ped.id_cliente;
~~~

-- 8 Obtener el cliente con el mayor gasto total 

~~~mysql
SELECT cli.nombre, SUM(ped.total)
FROM clientes AS cli
INNER JOIN pedidos AS ped
ON ped.id_cliente = cli.id
GROUP BY cli.nombre
ORDER BY SUM(ped.total) DESC LIMIT 1;
~~~

-- 9 Mostrar el pedido más reciente de cada cliente

~~~mysql
SELECT cli.nombre, MAX(ped.fecha) AS 'Fecha'
FROM clientes AS cli
INNER JOIN pedidos AS ped
ON cli.id = ped.id_cliente
GROUP BY cli.nombre;
~~~

-- 10 Obtener el detalle de pedidos (menús y cantidades) para el cliente 'Juan Perez'.

~~~mysql
SELECT depe.id_pedido, men.nombre, depe.cantidad, depe.precio_unitario
FROM detalles_pedidos AS depe
INNER JOIN menus AS men
ON depe.id_menu = men.id
INNER JOIN pedidos AS ped
ON depe.id_pedido = ped.id
INNER JOIN clientes AS cli
ON cli.id = ped.id_cliente
WHERE cli.nombre = 'Juan Perez';
~~~


-- Procedimientos Almacenados

-- Crear un procedimiento almacenado para agregar un nuevo
-- cliente
-- Enunciado: Crea un procedimiento almacenado llamado AgregarCliente que reciba como
-- parámetros el nombre, correo electrónico, teléfono y fecha de registro de un nuevo cliente y lo
-- inserte en la tabla Clientes .

~~~mysql
DELIMITER $$ 

DROP PROCEDURE IF EXISTS AgregarCliente;
CREATE PROCEDURE AgregarCliente(
IN ac_nombre VARCHAR(100),
IN ac_correo_electronico VARCHAR(100),
IN ac_telefono VARCHAR(15),
IN ac_fecha_registro DATE)
BEGIN
INSERT INTO clientes (nombre, correo_electronico, telefono, fecha_registro)
VALUES (ac_nombre, ac_correo_electronico, ac_telefono, ac_fecha_registro);
END $$

DELIMITER ;

CALL AgregarCliente('Carlos Diaz', 'carlos.diaz@example.com','1234-5678-910' ,'2024-05-05');
~~~


--Crear un procedimiento almacenado para obtener los
--detalles de un pedido
--Enunciado: Crea un procedimiento almacenado llamado ObtenerDetallesPedido que reciba
--como parámetro el ID del pedido y devuelva los detalles del pedido, incluyendo el nombre del
--menú, cantidad y precio unitario.

~~~mysql
DELIMITER $$

DROP PROCEDURE IF EXISTS ObtenerDetallesPedido;
CREATE PROCEDURE ObtenerDetallesPedido(
IN odp_id_pedido INT
)
BEGIN 
SELECT men.nombre, depe.cantidad, depe.precio_unitario 
FROM detalles_pedidos AS depe
INNER JOIN menus AS men
ON depe.id_menu = men.id
WHERE depe.id_pedido = odp_id_pedido;
END $$

DELIMITER ;


CALL ObtenerDetallesPedido(1);
~~~


--Crear un procedimiento almacenado para actualizar el
--precio de un menú
--Enunciado: Crea un procedimiento almacenado llamado ActualizarPrecioMenu que reciba
--como parámetros el ID del menú y el nuevo precio, y actualice el precio del menú en la tabla
--Menus

~~~mysql
DELIMITER $$

DROP PROCEDURE IF EXISTS ActualizarPrecioMenu;
CREATE PROCEDURE ActualizarPrecioMenu(
IN apm_id_menu INT,
IN apm_nuevo_precio INT
)
BEGIN 
UPDATE menus
SET precio = apm_nuevo_precio
WHERE id = apm_id_menu;
END $$

DELIMITER ;


CALL ActualizarPrecioMenu(1,2000);
~~~

--Crear un procedimiento almacenado para eliminar un cliente
--y sus pedidos
--Enunciado: Crea un procedimiento almacenado llamado EliminarCliente que reciba como
--parámetro el ID del cliente y elimine el cliente junto con todos sus pedidos y los detalles de los
--pedidos.

~~~mysql
DELIMITER $$

DROP PROCEDURE IF EXISTS EliminarCliente;
CREATE PROCEDURE EliminarCliente(
IN ec_cliente_id INT
)
BEGIN

DELETE FROM clientes
WHERE id = ec_cliente_id;
END $$

DELIMITER ;


CALL EliminarCliente(12);
~~~


--Crear un procedimiento almacenado para obtener el total
--gastado por un cliente
--Enunciado: Crea un procedimiento almacenado llamado TotalGastadoPorCliente que reciba
--como parámetro el ID del cliente y devuelva el total gastado por ese cliente en todos sus pedidos.

~~~mysql
DELIMITER $$

DROP PROCEDURE IF EXISTS TotalGastadoPorCliente;
CREATE PROCEDURE TotalGastadoPorCliente(
IN tgc_cliente_id INT
)
BEGIN
SELECT cli.nombre, SUM(ped.total) AS 'Total Gastado'
FROM clientes AS cli
INNER JOIN pedidos AS ped
ON cli.id = ped.id_cliente
WHERE cli.id = tgc_cliente_id
GROUP BY cli.nombre;

END $$

DELIMITER ;

CALL TotalGastadoPorCliente(1);
~~~













