# DOCUMENTACIÓN

**Índice**
1. [División](#id_division) 
2. [Pedidos](#id_pedidos)
 
 
<div id='id_division' />
 
### División
Para consultar la **división** de la empresa o cliente en cuestión se consulta la tabla **ipdivis**. Campo ***
 
```sql
SELECT * FROM ipdivis;
```
 
Un ejemplo de los datos que nos vamos a encontrar en esta tabla:
```
---
```
 
<div id='id_pedidos' />
 
### Pedidos
 
Los **pedidos** están formados por una **cabecera** y una o múltiples líneas. Esta información se almacena en la BBDD en las siguientes tablas:
 
```sql
select * from ipcabpe; -- Cabecera de pedido
select * from iplinpe; -- Líneas de pedido
```

#### Cabecera de pedido

#### Líneas de pedido

Un ejemplo de los datos que nos vamos a encontrar en esta tabla:
```
---
```
