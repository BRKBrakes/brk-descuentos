# BRK · Calculadora de Margen

App standalone (HTML/CSS/JS + Supabase). PWA instalable, sin build step.

## Desplegar en Vercel (primera vez)

1. Crea un repo nuevo y vacío en GitHub (ej: `brk-descuentos`), sin README.
2. En tu PC, dentro de esta carpeta (`brk-descuentos`), corre en VS Code / terminal:

```
git init
git add .
git commit -m "Version inicial calculadora BRK"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/brk-descuentos.git
git push -u origin main
```

3. Ve a https://vercel.com/new → "Import Git Repository" → selecciona `brk-descuentos`.
4. Framework Preset: **Other** (no necesita build, es estático). Deploy.
5. Vercel te da una URL tipo `brk-descuentos.vercel.app` — esa es la que usas de ahora en adelante.

## Actualizaciones futuras

Cada vez que yo te entregue cambios, reemplaza los archivos en esta carpeta y corre:

```
git add .
git commit -m "Descripcion del cambio"
git push origin main
```

Vercel hace auto-deploy en cada push (igual que el catálogo BRK).

## Instalar como PWA

Una vez desplegado en Vercel (PWA no funciona bien en `file://` local):
- **Android/Chrome:** menú → "Instalar app" / "Agregar a pantalla de inicio"
- **iPhone/Safari:** compartir → "Agregar a pantalla de inicio"
- **Windows/Mac (Chrome/Edge):** ícono de instalar en la barra de direcciones
