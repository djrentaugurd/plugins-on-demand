# plugins-on-demand

*[Read in English](README.md)*

Un plugin para Claude Code que mantiene tus otros plugins **desactivados hasta que una tarea los necesita**. Entonces activa el adecuado y lo vuelve a desactivar al terminar.

## Por qué

Cada plugin activado agrega las descripciones de sus skills, agents y comandos a **todas** las sesiones, los uses o no. Con unos pocos paquetes grandes puedes perder decenas de miles de tokens de contexto antes de escribir nada. Eso encarece las sesiones y le hace más difícil a Claude elegir la skill correcta.

`claude plugin details <plugin>` muestra lo que cuesta cada plugin en la línea `Always-on:`.

## Qué hace

- **Cataloga** tus plugins instalados: para qué sirve cada uno, cuántos tokens cuesta y si trae conectores (servidores MCP) que piden iniciar sesión.
- **Desactiva** todo lo que no fijes como siempre activo, después de preguntarte.
- **Activa** el plugin más pequeño que sirve cuando una tarea lo necesita, y te avisa que abras una sesión nueva para que cargue.
- **Lo desactiva** otra vez al terminar la tarea.
- Si quieres, escribe un índice corto en `~/.claude/CLAUDE.md`, para que las sesiones siguientes sepan qué plugin desactivado cubre cada tipo de tarea.

Solo activa y desactiva plugins. Nunca los instala, desinstala ni actualiza.

## Instalación

Dentro de Claude Code:

```
/plugin marketplace add djrentaugurd/plugins-on-demand
/plugin install plugins-on-demand@plugins-on-demand
```

O desde una terminal:

```bash
claude plugin marketplace add djrentaugurd/plugins-on-demand
claude plugin install plugins-on-demand@plugins-on-demand
```

## Uso

Abre una sesión nueva y di algo como:

> Configura los plugins bajo demanda.

Claude arma el catálogo, te muestra lo que cuesta cada plugin, te pregunta cuáles dejar siempre activos y desactiva el resto. Después trabaja con normalidad: cuando una tarea necesite un plugin desactivado, Claude lo activa y te pide abrir una sesión nueva. También puedes pedirlo directamente:

> Activa el plugin de SEO. / Apaga los plugins que activaste. / ¿Qué plugins están activos?

## Limitaciones

- **Activar un plugin tiene efecto en la siguiente sesión.** Claude Code carga los plugins al iniciar la sesión. Si no quieres reiniciar, Claude puede leer los archivos de la skill del plugin y seguirlos en la sesión actual.
- **Claude solo sabe de los plugins desactivados que le indicaste.** El índice opcional en `CLAUDE.md` es lo que permite que una sesión futura note que un plugin desactivado sirve para la tarea. Sin él, pide el plugin por nombre o por tema.
- **Los plugins con conectores pueden pedirte iniciar sesión** en un servicio externo al activarlos. La skill te avisa antes de activar uno.

## Estado

La skill guarda su estado en `~/.claude/plugins-on-demand/`:

| Archivo | Contenido |
|---|---|
| `catalog.md` | Una fila por plugin instalado: id, propósito, tokens fijos, conectores |
| `pinned.txt` | Plugins que siempre quedan activos |
| `enabled-by-me.txt` | Plugins que activó y todavía no apagó |

Son archivos de texto: puedes editarlos a mano. Borra la carpeta para empezar de cero.

## Requisitos

- Claude Code con el comando `claude plugin` (`enable`, `disable`, `list --json`, `details`).
- Funciona en Windows, macOS y Linux. Busca `claude` en el `PATH` y luego en `~/.local/bin/`.

## Licencia

[MIT](LICENSE)
