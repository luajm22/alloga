# DOCUMENTACIÓN

## Índice
- [División](#id_division) 
- [Pedidos](#id_pedidos)
	- Cabecera de pedido
	- Línea de pedido
 
 
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

Finalmente, se muestra contenido de ejemplo de esta tabla:

| CODDIV | ANOPED | CODPED     | SEQPED | ANOALB | CODALB   | ANOGRUP | CODGRUP | CODSERIEPREP | CODSERIEEXP | TIPOPED | SUPEDIDO | SUFECHA | INFOCLIEXTRA | CODCLI     | DESCLI                            | NIF         | CONTACTO | DIRECCION                      | DIRECCION1 | DIRECCION2 | DIRECCION3 | POBLACION | TELEFONO | CODPROV | DP    | PAIS | IDIOMA | MONEDA | NUMALBA | CODAREAEXPED | SWTANU | SWTRESERVASTOCK | SWTPREPARACION | SWTVALOR | SWTTOTAL | SWTPROFOR | SWTCUBETA | DESCUENTO | DESDESCUENTO | RECARGO | DESRECARGO | CONDPAGO | SWTGRUPO | PRIORIDADABS | PRIORIDADREL | URGENCIA | CODAGE | SWTPORTES | FECCAP  | HORACAP  | NLINEAS | SWTMODELO | REFMODELO | SWTREEXP | CODCLIREEXP   | DESCLIREEXP                         | NIFREEXP    | CONTAREEXP | DIRREEXP                                 | DIRREEXP1              | DIRREEXP2 | DIRREEXP3 | POBREEXP                 | PAISREEXP | TELREEXP | CODPROVREEXP | DPREEXP | CODDEMANDA | TIPODEMANDA | CODCOMEN | PESOPEDIDO | VOLPEDIDO | IMPTOTAL | FECSERVICIO | FECGRABACION | HORAGRABACION | FECRECEP | HORARECEP | FECAPER | HORAAPER | FECTERMIN | HORATERMIN | FECDEVUELTO | HORADEVUELTO | FECSALIDA | HORASALIDA | FECENTREGA | HORAENTREGA | STATUS | CODOPEMODIF | FECMODIF | HORAMODIF | PEDREC | IDMENSAVERI | TEXTOVERI | OBSERV1 | OBSERV2 | OBSERV3 | IVA | VALOR_IVA | COLCAMBIOVOL | HORARIO | CONDENT | CONTFAC | FORMAPAGO | DIASPAGO | CUENTABANC | DEVALB | INTFAH | FECHAH | HORAAH | FECFACTURA | SERIEFACT | SP | PERIFAC | CLICONT |
|--------|--------|------------|--------|--------|----------|---------|---------|--------------|-------------|---------|----------|---------|--------------|------------|-----------------------------------|-------------|----------|--------------------------------|------------|------------|------------|-----------|----------|---------|-------|------|--------|--------|---------|--------------|--------|-----------------|----------------|----------|----------|-----------|-----------|-----------|--------------|---------|------------|----------|----------|--------------|--------------|----------|--------|-----------|---------|----------|---------|-----------|-----------|----------|---------------|-------------------------------------|-------------|------------|------------------------------------------|------------------------|-----------|-----------|--------------------------|-----------|----------|--------------|---------|------------|-------------|----------|------------|-----------|----------|-------------|--------------|---------------|----------|-----------|---------|----------|-----------|------------|-------------|--------------|-----------|------------|------------|-------------|--------|-------------|----------|-----------|--------|-------------|-----------|---------|---------|---------|-----|-----------|--------------|---------|---------|---------|-----------|----------|------------|--------|--------|--------|--------|------------|-----------|----|---------|---------|
| 184    | 2023   | 0087092120 | 1      | 2023   | 61762830 | 0       | 0       | 396832       | 574937      | ZPN-L8  | 66028301 | 2460242 |              | 0000036831 | EL CORTE INGLES, S.A. (VALDEMORO) | ESA28017895 |          | CR ANDALUCIA K.23 MARGEN IZDA. |            |            | 26/10/2023 | VALDEMORO |          | 28      | 28340 | ES   | ES     |        | 0       | EXPED        | N      | N               | P              | S        | S        | N         | S         | 0         |              | 0       |            |          | N        | 99           | 99           | 99       | 010    | P         | 2460242 | 13:21:43 | 1       | J         | L8        | N        | 8422416200508 | EL CORTE INGLES, S.A. 920           | ESA28017895 |            | AVDA. FEDERICO SOTO, 1-3                 |                        |           |           | ALICANTE                 | ES        |          | 03           | 03003   | 10161906   | PEDIDOS     | 15098701 | 840        | 7020      | 0        | 2460244     | 2460242      | 13:30:22      | 2460242  | 13:30:30  | 2460242 | 18:19:39 | 2460243   | 09:57:37   | 2460243     |              | 2460243   | 11:23:23   | 2460245    | 15:31:00    | 20000  | VBFAESPED   | 2460243  | 11:30:51  |        | 0           |           |         | 15      | WE      | 0   | 0         |              |         | LF      | 0       |           | 0        |            | N      | N      |        |        | 0          |           |    |         |         |
| 184    | 2023   | 0087092119 | 1      | 2023   | 61762820 | 0       | 0       | 396832       | 574937      | ZPN-L8  | 66028301 | 2460242 |              | 0000036831 | EL CORTE INGLES, S.A. (VALDEMORO) | ESA28017895 |          | CR ANDALUCIA K.23 MARGEN IZDA. |            |            | 26/10/2023 | VALDEMORO |          | 28      | 28340 | ES   | ES     |        | 0       | EXPED        | N      | N               | P              | S        | S        | N         | S         | 0         |              | 0       |            |          | N        | 99           | 99           | 99       | 010    | P         | 2460242 | 13:21:44 | 1       | J         | L8        | N        | 8422416200508 | HIPERCOR BURGOS 726                 | ESA28017895 |            | CTRA. MADRID-IRUN KM 236                 |                        |           |           | BURGOS                   | ES        |          | 09           | 09001   | 10161905   | PEDIDOS     | 15098700 | 972        | 13723     | 0        | 2460244     | 2460242      | 13:30:22      | 2460242  | 13:30:30  | 2460242 | 18:19:39 | 2460243   | 09:57:17   | 2460243     |              | 2460243   | 11:23:23   | 2460245    | 15:31:00    | 20000  | VBFAESPED   | 2460243  | 11:30:49  |        | 0           |           |         | 15      | WE      | 0   | 0         |              |         | LF      | 0       |           | 0        |            | N      | N      |        |        | 0          |           |    |         |         |
| 184    | 2023   | 0087092118 | 1      | 2023   | 61762810 | 0       | 0       | 396832       | 574937      | ZPN-L8  | 66028301 | 2460242 |              | 0000036831 | EL CORTE INGLES, S.A. (VALDEMORO) | ESA28017895 |          | CR ANDALUCIA K.23 MARGEN IZDA. |            |            | 26/10/2023 | VALDEMORO |          | 28      | 28340 | ES   | ES     |        | 0       | EXPED        | N      | N               | P              | S        | S        | N         | S         | 0         |              | 0       |            |          | N        | 99           | 99           | 99       | 010    | P         | 2460242 | 13:21:44 | 1       | J         | L8        | N        | 8422416200508 | HIPERCOR CORNELLA 022/722           | ESA28017895 |            | COD.0022 SALVADOR DALI, 15-19            |                        |           |           | CORNELLA                 | ES        |          | 08           | 08940   | 10161904   | PEDIDOS     | 15098699 | 978        | 7020      | 0        | 2460244     | 2460242      | 13:30:23      | 2460242  | 13:30:30  | 2460242 | 18:19:38 | 2460243   | 09:56:50   | 2460243     |              | 2460243   | 11:23:23   | 2460245    | 15:31:00    | 20000  | VBFAESPED   | 2460243  | 11:30:47  |        | 0           |           |         | 15      | WE      | 0   | 0         |              |         | LF      | 0       |           | 0        |            | N      | N      |        |        | 0          |           |    |         |         |
| 184    | 2023   | 0087092117 | 1      | 2023   | 61762800 | 0       | 0       | 396832       | 574937      | ZPN-L8  | 66028301 | 2460242 |              | 0000036831 | EL CORTE INGLES, S.A. (VALDEMORO) | ESA28017895 |          | CR ANDALUCIA K.23 MARGEN IZDA. |            |            | 26/10/2023 | VALDEMORO |          | 28      | 28340 | ES   | ES     |        | 0       | EXPED        | N      | N               | P              | S        | S        | N         | S         | 0         |              | 0       |            |          | N        | 99           | 99           | 99       | 010    | P         | 2460242 | 13:21:45 | 1       | J         | L8        | N        | 8422416200508 | HIPERCOR CAMPO DE LAS NACIONES 019/ | ESA28017895 |            | SUC 719 AVDA. DE LOS ANDES, 50           |                        |           |           | MADRID                   | ES        |          | 28           | 28042   | 10161903   | PEDIDOS     | 15098698 | 840        | 7020      | 0        | 2460244     | 2460242      | 13:30:23      | 2460242  | 13:30:30  | 2460242 | 18:19:38 | 2460243   | 09:55:54   | 2460243     |              | 2460243   | 11:23:23   | 2460245    | 15:31:00    | 20000  | VBFAESPED   | 2460243  | 11:30:45  |        | 0           |           |         | 15      | WE      | 0   | 0         |              |         | LF      | 0       |           | 0        |            | N      | N      |        |        | 0          |           |    |         |         |


