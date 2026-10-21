# Analicis del modelo de atos 

## Identifocacion de modelos y tablas 
### res.partner
- nombre funcional: contacto
- modelo de Odoo: res.partner
- tabla PostgreSQL asociada: res_partner
- campos principales:company_name, email, name
- clave primaria: id
- principales claves ajenas; parent_id,country_id, user_id
- relaciones con otros objetos: country_id -> res.country
### product.product
nombre funcional: variante de producto
- modelo de Odoo: product.product
- tabla PostgreSQL asociada: product_product
- campos principales:bar code, active
- clave primaria: id
- principales claves ajenas: product_tmpl_id
- relaciones con otros objetos: product_tmpl

### sale.order
nombre funcional: pedido de venta
- modelo de Odoo: sale.order
- tabla PostgreSQL asociada: sale_order
- campos principales: name, date_order
- clave primaria: id
- principales claves ajenas: partner_invoice_id, user_id
- relaciones con otros objetos: res.user

### sale.order.line
nombre funcional: linea pedido de venta
- modelo de Odoo: sale.order.line
- tabla PostgreSQL asociada: sale_order_line
- campos principales: name, secuence
- clave primaria: id
- principales claves ajenas: order_id, salesman_id
- relaciones con otros objetos: sale.order


### tabla de modelos 

```sql
SELECT table_name
FROM information_schema.tables
WHERE table_schema = 'public'
	AND table_name IN ('res_partner', 'sale_order', 'sale_order_line', 'product_template', 'product_product')
	ORDER BY table_name;
```

### Columnas Principales

```sql
SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_schema = 'public'
	AND table_name = 'sale_order_line'
	ORDER BY ordinal_position;
```

### Claves Primarias 
```sql
SELECT conrelid::regclass AS tabla, conname, pg_get_constraintdef(oid) AS definicion
FROM pg_constraint
WHERE contype = 'p'
	AND conrelid IN ('res_partner'::regclass, 'sale_order'::regclass,
	'sale_order_line'::regclass, 'product_template'::regclass,
	'product_product'::regclass);

```
## Explicar la estructura de modelo

- Que es un modelo en odoo: es una clase python ( ej. res.partner)
- Que relacion existe entre modelo y tabla psotgresql: ORM crea y mantiene automaticamente una tabla por cada modelo. (res.partner -> res_partner)
-Funcion de una clave primaria: id -> identifica cada registro de forma unica.
- Funcion de una clave ajena: puede ser una clumna may2one, que en la base de datos es una clumna*_id
- Concepto Integridad referencial: es que cual quier clave ajena apunte a un registro que existe.
```
Segmento comercial (x_segmento_comercial)
| 1:N [res.partner.x_segmento_comercial_id]
 -- cliente (res_partner)
  |
    -- Pedido de venta (sale_order)
      |
        -- linea de pedido (sale_order_line)
            |1:N (sale_order_line_id)
             -- linea de pedido (sale_order_line)
             | 1:N `[product_id]-- servicio (product_product
|   |   |   |       | -- 1:N [product_tmpl_id] -- 
                    Product_template, type:'service')

 
```

## Explicar herramienta utilizadas 
- ORM de odoo: traduce modelo de python a tabla
- Modelos y campos del diccionario de odoo: guarda como resgistro los modelos y campos del sistema
- Interfaz tecnica de odoo: editar el diccionario: modelos, campos, vistas
- postgreSQL 16: sistema de gestor de datos
