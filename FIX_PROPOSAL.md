# Propuesta de fix: validación de email

Relacionado con #1.

## Problema

El validador de email actual usa (o asume) un regex demasiado permisivo, del estilo:

```
^[^\s@]+@[^\s@]+$
```

Esto solo exige un `@` con contenido no vacío a cada lado, sin validar la estructura interna del dominio. Como resultado, acepta direcciones inválidas como `usuario@.com`, `usuario@dominio..com` o `usuario@dominio` (sin TLD).

## Fix propuesto

```
^[^\s@]+@[^\s@.]+(\.[^\s@.]+)+$
```

### Por qué corrige el bug

- `[^\s@.]+` justo después de `@` impide que el dominio empiece con `.` → rechaza `usuario@.com`
- cada segmento entre puntos debe ser no vacío → rechaza `usuario@dominio..com`
- exige al menos un grupo `(\.[^\s@.]+)+` → rechaza `usuario@dominio` (sin TLD)
- sigue aceptando direcciones válidas como `usuario@dominio.com` y `usuario@sub.dominio.com`

## Casos de prueba sugeridos

| Email | Resultado esperado |
|---|---|
| `usuario@.com` | inválido |
| `usuario@dominio..com` | inválido |
| `usuario@dominio` | inválido |
| `usuario@dominio.com` | válido |
| `usuario@sub.dominio.com` | válido |
| `usuario.tag+algo@dominio.co` | válido |

## Nota

Este repositorio es un entorno de pruebas y no contiene una implementación real de validación de email (confirmado vía `search_code`). Este documento sirve como la propuesta de fix a aplicar en el codebase real correspondiente.
