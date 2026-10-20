Minimarket Mass Arequipa

Base de datos MySQL de una cadena de 3 minimarkets Mass en Arequipa (Cayma, Cerro Colorado y Yanahuara).
NRC 16211 · Diseño: 16 oct 2026 · Implementación: 20 oct 2026


Estructura

minimarket-mass/
├── docs/
│   ├── diagrama_er.png        # Diagrama ER (20 tablas, cardinalidades visibles)
│   ├── documento_diseno.pdf   # Documento de diseño (10 secciones)
│   └── modelo.mwb             # Modelo Workbench (exportar con Database > Reverse Engineer desde schema.sql)
├── database/
│   ├── schema.sql             # Tablas, claves, restricciones, triggers, procedimientos y vista
│   ├── datos.sql              # 3 tiendas, 20 productos, 12 usuarios, 13 ventas
│   └── consultas.sql          # Las 8 consultas + bonus IGV por mes
├── .gitignore
└── README.md

Cómo restaurar la base

Requisitos: MySQL 8.0.16 o superior (necesita CHECK y columnas generadas). Ejecutar en este orden:


mysql -u root -p < database/schema.sql
mysql -u root -p < database/datos.sql
mysql -u root -p < database/consultas.sql

schema.sql borra y crea la base minimarket_mass. Todos los usuarios de ejemplo tienen la clave password (hash bcrypt, solo demo).


Tablas (20)

Módulo	Tablas
Ubicación y personal	distrito, tienda, usuario
Clientes	cliente
Catálogo	categoria, marca, unidad_medida, producto
Inventario	stock, movimiento_stock, merma
Caja	caja, caja_turno
Ventas	venta, detalle_venta, anulacion_venta
Pagos	pago_venta, pago_efectivo, pago_tarjeta, pago_billetera

Las 12 de la consigna se mantienen; se agregaron marca, unidad_medida, caja, merma, anulacion_venta y los tres detalles de pago.


Reglas de negocio

Regla	Implementación
1 Comprobantes	UNIQUE (serie, correlativo); series por tienda con CHECK; correlativo automático (correlativo = 0)
2 Clientes	cliente_id nulo en boleta; factura exige cliente con RUC (CHECK + trigger); DNI 8 y RUC 11 dígitos
3 Precio e IGV	un precio por producto; CHECK total = subtotal + igv
4 Anulación	sin DELETE (trigger); sp_anular_venta registra, anula pagos y devuelve stock
5 Stock	CHECK cantidad >= 0; kardex mediante sp_mover_stock
6 Pagos	ENUM medio_pago; vista vw_control_ventas (OK / DESCUADRE)
7 Caja	solo vende con turno ABIERTO de su tienda; un turno abierto por caja y por cajero
8 Usuarios	tienda_id obligatorio y ENUM rol

Convenciones de commits

Conventional Commits: feat:, docs:, fix:, chore:.


Integrantes


Alexander Emmanuel Moreno Flores (trabajo individual)


Uso académico · NRC 16211

