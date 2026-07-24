# BRK · Calculadora de Margen — Arquitectura

## Qué es
App standalone (single HTML file + Supabase) para calcular margen y descuentos por código BRK.
Uso exclusivo de Carlos Gómez — no es una herramienta de equipo.

## Stack
- Frontend: HTML/CSS/JS vanilla, sin build step, un solo archivo `index.html`
- Backend: Supabase (proyecto compartido "BRK", `ytgziytuldsqhymqjyig`), schema **`descuentos`** (aislado de `public`, que pertenece al Smart Catalog — nunca tocar tablas fuera de este schema)
- Hosting: Vercel, auto-deploy on push a `main` (repo `BRKBrakes/brk-descuentos`)
- Auth: Supabase Auth (email/password), usuario único `carlos.gomez@brkbrakes.com`
- Librerías: SheetJS (xlsx) para leer Excel, ExcelJS para generar el export con estilos

## Esquema de datos (`descuentos`)
| Tabla | Propósito |
|---|---|
| `productos_descuento` | Tabla principal: codigo_brk, costo, lv1, lv2, mt1, familia, marca, notas, fecha_actualizacion |
| `admin_auth` | Hash de la clave de Admin/Exportar (bcrypt vía pgcrypto) |
| `historial_cargas` | Log de cada carga completa (fecha + total de registros) |
| `ultima_carga_tipo` | Última fecha de subida por tipo de archivo (costos/lv1/lv2/mt1) |
| `backups` | Snapshot completo de `productos_descuento` antes de cada carga (1 nivel de reversión) |

## Funciones RPC (todas `security definer`, grant a `authenticated` únicamente)
- `get_producto(p_codigo)` — búsqueda pública dentro de la app
- `upsert_productos(p_clave, p_data)` — carga masiva, usa `coalesce` para no borrar campos no incluidos en el archivo subido
- `crear_backup(p_clave)` / `revertir_ultima_carga(p_clave)` — respaldo y reversión
- `exportar_productos(p_clave)` — exportación completa (protegida con clave adicional sobre la sesión)
- `validar_clave`, `registrar_carga`, `actualizar_carga_tipo`, `listar_historial`, `listar_ultima_carga`

## Reglas de negocio clave
- **Margen, no markup:** `(precio_venta - costo) / precio_venta`
- **Semáforo:** rojo <40%, naranja 40-50%, verde >50%
- **Familias permitidas** (todo lo demás se descarta al subir Excel): Pastillas, Discos, Cilindros, Campanas, Zapatas, Líquido de frenos, Grasas y limpiadores, Bandas, Sensores
- **Fuente de datos:** exports manuales de SIESA (Costos.xls, LV1.xls, LV2.xls, MT1.xls) — sin conexión API directa (pendiente de aprobación)
- Referencia se limpia quitando prefijo `PC` y espacios antes de cruzar con costo/precios

## Regla fija (igual que Smart Catalog)
Cualquier cambio estructural (tabla nueva, función RPC nueva, decisión de seguridad, cambio de arquitectura) debe venir con la actualización correspondiente de este documento en el mismo commit/entrega.
