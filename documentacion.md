# DOCUMENTACIÓN

## Índice

- [Estructura BBDD](#id_estructura)
	- [División](#id_division) 
	- [Pedidos](#id_pedidos)
		- Pedido Origen
		- Pedido Final
		- Cabecera de pedido
	- [Facturas](#id_facturas)
	- [Status](#id_status)
- [Incidencias](#id_incidencias)
	- [Error: No consiguen facturar](#id_incidencia_no_facturar)
- [Rutas de utilidad](#id_rutas)

 
<div id='id_estructura' />

## Estructura BBDD

<div id='id_division' />
 
### División
Para consultar la **división** de la empresa o cliente en cuestión se consulta la tabla **ipdivis**. 

```sql
select * from ipdivis;
```
Un ejemplo de los datos que nos vamos a encontrar en esta tabla:

  
| campo1 | campo2 | campo3 | campo4 | campo5 | 
|-----------|-----------|-----------|-----------|-----------|
| contenido | contenido | contenido | contenido | contenido | 
| contenido | contenido | contenido | contenido | contenido |
| contenido | contenido | contenido | contenido | contenido |
 
<div id='id_pedidos' />
 
### Pedidos
 
Los **pedidos** están formados por una cabecera y una o múltiples líneas. Esta información, que desglosaremos y detallaremos en secciones posteriores, se almacena en la BBDD en las siguientes tablas:
 
```sql
select * from ipcabpeorig; -- Cabecera de pedido origen
select * from iplinpeorig; -- Líneas de pedido origen

select * from ipcabpe; -- Cabecera de pedido
select * from iplinpe; -- Líneas de pedido
```
<div id='id_pedido_origen' />

#### Pedido Origen
El pedido origen es el resultado de la carga de datos inicial de un determinado pedido facilitado por la empresa. 

Este pedido puede facilitarse en distintos formatos (**txt**, **xlsx**, **csv**...) y ha de ser registrado en la BBDD. En este momento no se cuenta con todos los datos necesarios para conformar el pedido, pues muchos de ellos son **calculados** y será necesario ejecutar determinados procesos en la BBDD para crear el pedido final.

```sql
select * from ipcabpeorig; -- Cabecera de pedido origen
select * from iplinpeorig; -- Líneas de pedido origen
```

#### Pedido Final
Una vez cargado el [pedido origen](#id_pedido_origen) se harán los procedimientos almacenados y serán calculados todos los campos necesarios para conformar el pedido. 

```sql
select * from ipcabpe; -- Cabecera de pedido
select * from iplinpe; -- Líneas de pedido
```
#### Cabecera de pedido
A continuación se muestran los campos que forman la **clave primaria** de la cabecera del pedido.
<table>
<tr>
        <th>Campo BBDD</th>
        <th>Descripción</th>
    </tr>
    <tr>
        <td>codped</td>
        <td>Código del pedido</td>
    </tr>
    <tr>
        <td>coddiv</td>
        <td>División o compañía del pedido</td>
    </tr>
    <tr>
        <td>seqped</td>
        <td>Versión del pedido</td>
    </tr>
    <tr>
        <td>anoped</td>
        <td>Año del pedido</td>
    </tr>  
</table>

<div id='id_status' />

### Status

En esta tabla podemos ver la relación entre cada status y su descripción.

Ejemplo:

Al consultar una línea de pedido, podemos ver su status en el campo **status**. 

Para conocer la descripción de este estado debemos consultar la tabla **ipstatus**. 

```sql
SELECT tipostatus, status, desstatus
  FROM ipstatus
 WHERE tipostatus = 'LP' -- Línea de Pedido
   AND status = '500'; -- Status
```

Un ejemplo con el contenido de la tabla podría ser el siguiente:

| tipostatus | status | desstatus |
|-----------|-----------|-----------|
| LP | 5000 | LINEA DE ACONDICIONAMIENTO | 
| CP | -6950 | ERROR GENERANDO DEM. DE ACONDI | 

<table>
	<tr>
        <th>Tipo Status</th>
        <th>Descripción</th>
    </tr>
	<tr>
        <td>LP</td>
        <td>Línea de Pedido</td>
    </tr>
	<tr>
        <td>CP</td>
        <td>Cabecera de Pedido</td>
    </tr>
	<tr>
        <td>IRS</td>
        <td>Impresiones (Facturas)</td>
    </tr>
	<tr>
        <td>CP</td>
        <td>Cabecera de Pedido</td>
    </tr>
</table>


<div id='id_incidencias' />

## Incidencias


<div id='id_incidencia_no_facturar' />

### Error: No consiguen facturar (INCYTE)

Para verificar que efectivamente, la factura que indican no ha sido facturada, debemos consultar el campo **contfac** en la tabla **ipcabped**. Si este campo tiene un valor distinto de 0, se ha podido facturar.

```sql
SELECT contfac
  FROM ipcabped
 WHERE coddiv = ''
   AND codped = '';
```

En caso de que el campo **contfac** tenga el valor 0, debemos comprobar el estado de las líneas de pedido. En caso de que una (o más) líneas tengan **status** igual a 100, el pedido no facturará.

```sql
SELECT contfac
  FROM iplinped
 WHERE coddiv = ''
   AND codped = ''
   AND status = 100;
```

Con esta última consulta SQL podremos ver qué lineas están haciendo que no se pueda facturar el pedido.

Generalmente los clientes utilizan el traductor **frmMain.frm** para facturar los pedidos, aquí podemos ver qué comprobaciones están haciendo sobre las líneas, que pueden hacer que finalicen con error.

```sql
DECLARE
	v_coddiv	ipcabpe.coddiv%TYPE;
	v_codped	ipcabpe.codped%TYPE;

	v_contfac	ipcabpe.contfac%TYPE;
	v_count		NUMBER;
BEGIN

	------------------------------------
	--        DATOS INCIDENCIA        --
	------------------------------------

	v_coddiv := ''; -- División
	v_codped := ''; -- Código de pedido
	
	------------------------------------

	BEGIN
		SELECT contfac
		INTO v_contfac
		FROM ipcabpe
		WHERE coddiv = v_coddiv
		AND codped = v_codped;
	EXCEPTION
		WHEN NO_DATA_FOUND THEN
			dbms_output.put_line('No se ha encontrado el pedido indicado.');
			RETURN;
		WHEN TOO_MANY_ROWS THEN
			dbms_output.put_line('Varios pedidos encontrados.');
			RETURN;
	END;

	IF v_contfac != 0 THEN
		dbms_output.put_line('El pedido ya se ha podido facturar.');
		RETURN;
	END IF;

	SELECT COUNT(1)
	  INTO v_count
	  FROM iplinpe
	 WHERE coddiv = v_coddiv
	   AND codped = v_codped
	   AND status = 100;
	
	dbms_output.put_line('Se han encontrado ' || v_count || ' linea/s con status 100.');
	dbms_output.put_line('');
	dbms_output.put_line('SELECT *');
    dbms_output.put_line('FROM iplinpe');
	dbms_output.put_line('WHERE coddiv = ''' || v_coddiv || '''');
	dbms_output.put_line('AND codped = ''' || v_codped || '''');
	dbms_output.put_line('AND status = 100;');
END;
```
<div id='id_rutas' />

## Rutas de utilidad

<table>
	<tr>
        <th>Nombre</th>
        <th>URL</th>
		<th>Observaciones</th>
    </tr>
	<tr>
        <td>Productividad</td>
        <td>C:\Programacion\Productividad-Oracle</td>
		<td></td>
    </tr>
	<tr>
        <td>Reports .rpt (Crystal Report)</td>
        <td>I:\proyectos\T2PICKING\IP6\RPT</td>
		<td>Acceso a través de borox-ts-prd</td>
    </tr>
	<tr>
        <td>Facturas</td>
        <td>J:\wwwroot\xls\EXTRANET</td>
		<td>Contiene todas las facturas impresas en la última hora. Un proceso automático limpia la carpeta cada hora. Acceso a través de borox-ts-prd</td>
    </tr>
	<tr>
        <td>Traductores</td>
        <td>C:\Programacion</td>
		<td>Acceso a través de borox-ts-prd</td>
    </tr>
</table>
