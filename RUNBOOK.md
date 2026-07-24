# BRK · Calculadora de Margen — Runbook

## Actualizar precios/costos (uso normal)
1. Exporta de SIESA: Facturación/Reportes de Costos, LV1, LV2, MT1 (formato .xls tal como los entrega SIESA)
2. Entra a la app → Admin (pide clave)
3. Sube los archivos que hayan cambiado (no es obligatorio subir los 4 cada vez)
4. "Procesar y subir" — la app filtra por familia, limpia el prefijo `PC`, combina por código y hace upsert
5. Verifica el mensaje de "X referencias actualizadas" y revisa el Historial de cargas

## Si subiste un archivo malo
1. Ve a Admin → **"Revertir última carga"** (pide clave de nuevo)
2. Esto restaura el estado exacto de antes de esa carga — **solo 1 nivel hacia atrás**, no hay pila de reversiones
3. Si ya hiciste una segunda carga después del error, revertir solo deshace la última, no la que falló antes

## Si "Exportar" da error
- Confirma que corriste el SQL de `exportar_productos` con tu clave real (no el placeholder)
- Si dice "Clave incorrecta o error al exportar" y estás seguro de la clave, revisa consola del navegador — el mensaje se oculta a propósito por seguridad

## Si la búsqueda da error de permisos (`permission denied for schema descuentos`)
Causa típica: se agregó un rol nuevo (ej. al pasar de `anon` a `authenticated` para el login) y falta:
```sql
grant usage on schema descuentos to authenticated;
```

## Si cambias de `anon` a otro rol de auth
Recuerda que TODAS las funciones RPC deben tener `revoke ... from anon` + `grant ... to authenticated` — si falta una función, esa acción específica fallará con error de permisos aunque el resto funcione.

## Login
- Usuario único: `carlos.gomez@brkbrakes.com`, gestionado en Supabase Authentication → Users
- Sesión vive en `sessionStorage`: sobrevive a `Ctrl+R`, se borra al cerrar el navegador completamente
- Botón "Salir" cierra sesión manualmente

## Actualizar el código (index.html)
Los datos viven en Supabase, no en el HTML — reemplazar el archivo NUNCA borra datos.
```
git add .
git commit -m "descripcion del cambio"
git push origin main
```
Vercel hace auto-deploy.

## Límite de filas de la API (importante si la base sigue creciendo)
Supabase por defecto limita respuestas a 1000 filas (`Project Settings → Data API → Max Rows`).
Ya está configurado en 20000 — si algún día la base supera eso, subir el límite ahí.
