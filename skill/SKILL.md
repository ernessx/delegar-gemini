---
name: delegar-gemini
description: Delega tareas rutinarias y faciles de verificar a Gemini mediante la terminal agy (suscripcion, canal privado) para ahorrar tokens de Claude. Usar cuando la tarea sea repetitiva, acotada y se pueda comprobar con un comando; no para decisiones delicadas, diagnosticos ni nada con riesgo.
---

# Delegar a Gemini con `delegar-gemini`

## Cuando delegar
- SI: tareas repetitivas y acotadas (documentar, resumir secciones, buscar, generar archivos de configuracion con una herramienta propia) cuyo resultado se pueda comprobar con un comando.
- NO: diagnosticos sin causa clara, cableado o seguridad, decisiones de diseno, compras, cambios de infraestructura, cualquier cosa con riesgo.

## Como
1. Escribe el prompt en un archivo (pasos numerados, que NO haga, que resuma al final en pocas lineas).
2. Lanzalo EN SEGUNDO PLANO (puede tardar varios minutos) y lee solo el informe final:
   `delegar-gemini libre /ruta/prompt.md --comprobar "comando que sale con 0 si todo esta bien" --nombre "etiqueta"`
   En Windows no existe `test`: para el comando de comprobacion usa `python -c ...` o `if exist ...`.
3. Interpreta el veredicto:
   - VERIFICADO: la comprobacion paso; no hace falta revisar a mano (salvo la PRIMERA vez de un tipo de tarea nuevo: revisarla una vez).
   - FALLO LA COMPROBACION o SIN COMPROBACION AUTOMATICA: revisa tu el resultado y corrige.
4. `delegar-gemini historial` muestra las ultimas delegaciones.

## Reglas
- Usa siempre `agy` (suscripcion). No uses la API gratuita de Gemini: comparte los datos con Google.
- No des a Gemini comandos genericos (`cat`, `grep`, `sudo`): prefiere una herramienta acotada que expongas tu.
- Nunca incluyas claves o secretos en el prompt.
- No confies en el relato de Gemini ("ya lo hice"): comprueba contra el estado real.
