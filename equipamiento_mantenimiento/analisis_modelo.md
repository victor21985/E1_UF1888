## identificacion de modelos y tablas
### objetivo
identificar los modelos de odoo implicados en el ejercicio y comprobar sus correspondientes tablas PostgreSQL 
### consulta realizada 

```sql
SELECT id, model, state
FROM public.ir_model
WHERE model IN(	
	'res.parnert','maintenance.equipament', 'maintenance.category', 'maintenance.request',
	'maintenance.stage', 'maintenance.team'

)

ORDER BY model;
```

### conexion de datos 

```sql
SELECT current_database() AS base_de_datos,
	current_user AS usuario,
	version() AS version_postgresql;
