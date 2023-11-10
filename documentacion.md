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
	- [Contadores de facturación para facturas, abonos y rectificativas (BIOCON)](#id_incidencia_contador_facturacion)
	- [Generación de carpetas](#id_incidencia_generacion_carpeta)
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

A continuación se muestran algunos de los tipos más utilizados:
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
</table>


<div id='id_incidencias' />

## Incidencias


<div id='id_incidencia_no_facturar' />

### Error: No consiguen facturar (INCYTE)

Para verificar que efectivamente, la factura que indican no ha sido facturada, debemos consultar el campo **contfac** en la tabla **ipcabpe**. Si este campo tiene un valor distinto de 0, se ha podido facturar.

```sql
SELECT contfac
  FROM ipcabpe
 WHERE coddiv = ''
   AND codped = '';
```

En caso de que el campo **contfac** tenga el valor 0, debemos comprobar el estado de las líneas de pedido. En caso de que una (o más) líneas tengan **status** igual a 100, el pedido no facturará.

```sql
SELECT contfac
  FROM iplinpe
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

Una vez que se han identificado las líneas con error, deberíamos recurrir al informador e indicarle lo que sucede para cada artículo (para cada una de las líneas con status 100).

Errores frecuentes:
- "Este artículo no ha sido reportado"
- "Las cantidades reales y reportadas no coinciden"
	- Campos: En la línea de pedido **cantaservir** y **cantservida** deben tener el mismo valor.

Ruta de interés - Documentos Reportados
```
I:\FICHEROSIP6\CLIENTES\INCYTE\ENTRADA\BAK
```

La consulta que hace el traductor para ver los pedidos a facturar es la siguiente:

```sql
SELECT * 
  FROM ipcabpe CP 
 WHERE CODDIV='138' 
   AND STATUS=18999 
   AND NOT EXISTS (SELECT 1 
   					 FROM IPLINPE LP 
					WHERE LP.CODPED=CP.CODPED 
					  AND LP.CODDIV=CP.CODDIV 
					  AND LP.ANOPED=CP.ANOPED 
					  AND LP.SEQPED=CP.SEQPED 
					  AND LP.STATUS=100);
```

<div id='id_incidencia_contador_facturacion' />


### Contadores de facturación para facturas, abonos y rectificativas (BIOCON)

El objetivo de esta incidencia es configurar los contadores cuando hay un nuevo laboratorio o un nuevo cliente. Son genéricos, lo único que cambia será la división en función del cliente al que haya que configurarle los contadores. En los **INSERT** que se muestran a continuación, utilizamos la división **604**, de **BIOCON**.

```sql
Insert into T2P.XIPCONTFAC (DIVISION,NUMINI,PERIODO,SERIE,NUMACTUAL,TIPO,ABIERTO,TIPOPEDIDO,MASCARA) values ('604','1','2023','PP','2300001','F','S',null,null);
Insert into T2P.XIPCONTFAC (DIVISION,NUMINI,PERIODO,SERIE,NUMACTUAL,TIPO,ABIERTO,TIPOPEDIDO,MASCARA) values ('604','1','2023','PA','2390001','A','S',null,null);
Insert into T2P.XIPCONTFAC (DIVISION,NUMINI,PERIODO,SERIE,NUMACTUAL,TIPO,ABIERTO,TIPOPEDIDO,MASCARA) values ('604','1','2023','FI','2380001','R','S',null,null);
```

<div id='id_incidencia_generacion_carpeta' />

### Generación de carpetas

El objetivo de esta incidencia es generar las carpetas cuando hay un nuevo laboratorio o un nuevo cliente. Son genéricas, lo único que cambia será la división en función del cliente al que haya que generarle las carpetas.

Carpeta en la que se generan los pdfs para el proceso diario de envío de ZIP.
```
J:\wwwroot\xls\604\FACTURAS\OUT
```

Para el envío y la recepción de archivos (Integración con el nuevo cliente).
```
I:\IP6DATOS\INTERFASES\BIOCON
I:\IP6DATOS\INTERFASES\BIOCON\bajar
I:\IP6DATOS\INTERFASES\BIOCON\subir
I:\IP6DATOS\INTERFASES\BIOCON\errores
```

<div id='id_estructura_nombre_rpt' />


### Estructura del nombre de los reportes

El nombre de los reportes debe seguir la siguiente estructura:

"IPALBARAN" + TIPO + CODIGO DIVISION

Un ejemplo de nombre de reporte podría ser: **IPALBARANV160.rpt**

<table>
	<tr>
        <th>Tipo</th>
        <th>Descripción</th>
    </tr>
	<tr>
        <td>V</td>
        <td>ALBARAN</td>
    </tr>
	<tr>
        <td>F</td>
        <td>FACTURA</td>
    </tr>
	<tr>
        <td>P</td>
        <td>PROFORMA</td>
    </tr>
</table>


<div id='id_subir_visual_a_produccion' />

### Subir Visual a producción

1. Modificamos el archivo original **.frm** en el ambiente de pruebas.
2. Se crea el ejecutable **.exe** en el ambiente de pruebas, que es donde hemos hecho nuestras modificaciones. 
Para crear el ejecutable: Archivo > Generar **nombre_ejecutable**
Es necesario crear una copia de seguridad del ejecutable anterior.
3. Se copia el **.frm** en el SVN

En el servidor **borox-ts-prd** están ejecutándose 4 procesos: Productividades, Informes, Vincilab y SII-AH.

Si se hace una mopdificación en alguno de estos 4 procesos, es necesario realizar las modificaciones en el servidor **borox-ts-prd**.

En caso de no modificar ninguno de estos 4 procesos, realizaríamos las modificaciones en:

```
\\10.20.4.38  Producción
```

En el servidor Producción, hacemos un comentario (#) a la línea del ejecutable correspondiente en la ruta 
```
C:\Programacion\Aplicaciones
```
El archivo a modificar es **apps.ini.**  (Guardo el archivo).
 
Renombrar con la fecha del día el .exe que se encuentra productivo y lo CORTAR y pegar en \\10.20.4.38\Programacion\Aplicaciones\Versiones antiguas
 
Copiar el .frm nuevo modificado del SVN y dejarlo en la carpeta correspondiente del traductor
\\10.20.4.38\Programacion\
 
Copiar el .exe en la carpeta \\10.20.4.38\Programacion\Aplicaciones\
 
Quitar comentario (#) a la línea del ejecutable correspondiente C:\Programacion\Aplicaciones
archivo apps.ini.  (Guardo el archivo).
 
Validar que el programa quede ejecutándose. 



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
		<td>Traductor de productividades. Acceso a través de borox-ts-prd.</td>
    </tr>
	<tr>
        <td>Reports .rpt (Crystal Report)</td>
        <td>I:\proyectos\T2PICKING\IP6\RPT</td>
		<td>Acceso a través de borox-ts-prd.</td>
    </tr>
	<tr>
        <td>Facturas</td>
        <td>J:\wwwroot\xls\EXTRANET</td>
		<td>Contiene todas las facturas impresas en la última hora. Un proceso automático limpia la carpeta cada hora. Acceso a través de borox-ts-prd.</td>
    </tr>
	<tr>
        <td>Traductores</td>
        <td>C:\Programacion</td>
		<td>Acceso a través de borox-ts-prd.</td>
    </tr>
	<tr>
        <td>Documentos Reportados</td>
        <td>I:\FICHEROSIP6\CLIENTES\INCYTE\ENTRADA\BAK</td>
		<td>En ENTRADA quedan todos los documentos reportados por el cliente. A BAK pasarían todos los que hayan sido procesados.</td>
    </tr>
</table>

