##  Modificacion del diicionario de datos 

### 1. situacion inicial
el modelo (res.partner) no permitia clasificar cormecialmentea los clientes. Semodifica res.partner y se crea el modelo x_segmento_comercial.

### 2. objetos creados
x_nivel_cliente        res.partner                 seleccion
segmento comercial     x_segmento_comercial       modelo nuevo 
x_name                 x_segmento_comercial       char
x_codigo               x_segmento_comercial       char
x_descripcion          x_segmento_comercial       text
x_segmento_comercial_id res.partner               many2one segmento_comercial

### 3. relacion y justifiacion 
cliente N: 1 segmento_comercial. un cliente pertenece a un unico segmento y un segmento agrupa a muchos clientes. Por eso un campo may2one en res.partner, que genera la clave ajena x_segmento_comercial_id - x_segmento_comercial.id



segmento comercial - 1N - cliente -1N- pedido de venta -1N- linea de pedido -1N- servicio.

### a. Restricciones de integridad
Restrinciones de implemenracion problema evita
Nombre y codigo obligatorio  Atributo Obligaatorio (NOT NULL) segmento sin identificar
Codigo unico Regla de automatizacion (base automaation)  Codigo duplicados y clasificacion ambigua 
Borrado restringido ON DELTE RESTRIC Cliente apuntado a segmento inexistente 

Regla de automatizacion: Modelo segmento comercial, disparador Al crear y actualizar, campo codigo, accion de ejecutar codigo:
```sql
python
for rec in records:
    if rec.x_codigo and env['x_segmento_comercial'].search_count(
            [('x_codigo', '=ilike', rec.x_codigo.strip()), ('id', '!=', rec.id)]):
        raise UserError("Ya existe un segmento con el código '%s'." % rec.x_codigo)

```

### 5. Permisoso y vista heredada 
Grupo                    | Leer | Escribir | Crear | Eliminar |
| ------------------------ | ---- | -------- | ----- | -------- |
| Administración / Ajustes | ✔    | ✔        | ✔     | ✔        |
| Usuario interno          | ✔    | ✔        | ✔     | ✘        |
 
Vista heredada res.partner.form.segmento: hereda de base.view_partner_form, en modo Extensión.
```xml
<data>
    <xpath expr="//notebook" position="inside">
        <page string="Clasificación comercial" name="clasificacion_comercial">
            <group>
                <field name="x_nivel_cliente"/>
                <field name="x_segmento_comercial_id"/>
            </group>
        </page>
    </xpath>
</data>
```

### 6. Procedimiento (modo desarrollador)
Activar el modo desarrollador (/odoo?debug=1) e instalar Reglas de automatización.
Técnico › Campos: crear x_nivel_cliente en res.partner.
Técnico › Modelos: crear x_segmento_comercial y añadirle x_codigo y x_descripcion.
Técnico › Campos: crear x_segmento_comercial_id (many2one, Restringir).
Técnico › Automatización: crear la regla de código único.
Técnico › Seguridad › Derechos de acceso: crear los permisos.
Técnico › Vistas: crear la vista heredada.
Técnico › Acciones y Menús: crear el acceso a Segmentos comerciales.
 
### 7. Pruebas y resultado
####   Prueba  Resultado esperado  Obtenido
- P1    Abrir un cliente    Se ven los campos nuevos    [✔]
- P2    Crear segmentos Se guardan  [✔]
- P3    Asignar nivel y segmento a un cliente   Se guarda   [✔]
- P4    Segmento sin código No permite guardar  [✔]
- P5    Código duplicado (pyme) Aviso de la regla   [✔]
- P6    Borrar segmento con clientes    Error, no se borra  [✔]
- P7    Borrar segmento sin clientes    Se borra    [✔]
 
Resultado: el modelo de clientes queda ampliado y relacionado con los segmentos, y las restricciones funcionan. La verificación en PostgreSQL está en verificacion_postgresql.md.


