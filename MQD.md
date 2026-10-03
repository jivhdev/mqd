---
tipo: metodo
version: 2.0-borrador
fecha: 2026-10-03
---

# MQD v2

Método de Javier para definir qué construir y desarrollarlo con agentes de IA, gastando lo mínimo y sin perder el control.
Cada regla cita la decisión que la originó (D-NN, en [Hormiguero › DECISIONES](https://github.com/jivhdev/hormiguero/blob/main/definicion/DECISIONES.md)). Lo marcado **[PROPUESTA]** todavía no lo aprobó Javier.

## 0. Regla rectora

> La IA ejecuta, no decide. No asume. No completa por cuenta propia. Si falta información, se detiene y la pide.

Corolarios:
- Los **nombres** oficiales (ecosistema, apps, pantallas, conceptos) los elige Javier. Si falta uno, la IA se detiene, lo registra como Caso y lo pide.
- Los **hechos** los busca la IA; las **decisiones** las toma Javier (D-09).
- Se escribe en español neutro, sin voseo.

## 1. Roles

| Rol | Quién | Hace | No hace |
|---|---|---|---|
| Director | Javier | Cuenta la idea, aprueba pantallas y planes, prueba en hitos, decide lo que no está escrito (D-13) | No toca la terminal ni copia y pega entre IAs (D-15) |
| Arquitecto y revisor | Claude Code | Especifica, parte el trabajo en bloques, delega, revisa cada bloque, hace lo difícil (D-14) | No implementa lo que OpenCode puede hacer |
| Ejecutor | OpenCode (OpenCode Go) | Implementa bloques acotados con sus pruebas (D-14) | No decide arquitectura ni alcance: se detiene |

Claude dirige a OpenCode por terminal, con el bloque como archivo adjunto y una orden corta primero (D-15):

```
opencode run "Ejecuta exactamente el bloque del archivo adjunto" -m opencode-go/<modelo> --dir <carpeta> -f <bloque.md>
```

Pasar el bloque como texto en la línea de comandos falla en Windows (comillas y saltos de línea). Javier solo abre Claude Code.

## 2. Dónde vive cada cosa (D-38, D-40)

```
C:\JV                         todo lo de Javier, igual en todos sus equipos
├── MQD\                      este método (github.com/jivhdev/mqd, CC BY-SA 4.0)
└── <ecosistema>\             un monorepo por ecosistema (ej.: hormiguero, GPL v3)
    ├── AGENTS.md             reglas para cualquier agente (OpenCode lo lee siempre)
    ├── CLAUDE.md             "@AGENTS.md" + rol de Claude
    ├── ESTADO.md             último traspaso: dónde quedamos y qué sigue
    ├── tablero.base          tablero visual de Obsidian sobre bloques/ (D-34)
    ├── definicion\           decisiones y plan del ecosistema
    ├── semillas\<App>\       todo lo que define una app (sección 6)
    ├── bloques\              una nota por bloque de trabajo (sección 5)
    ├── src\  tests\          código
    └── .obsidian\            el monorepo se abre como vault de Obsidian
```

Fuera de `C:\JV` (y nunca en un repo): libros y referencias. Los documentos reales de prueba viven en `C:\JV\pruebas\`, fuera de todo repo: nunca se suben ni se pegan en una IA (D-10, D-11, D-46).

Todo cambio se respalda en GitHub al cerrar cada bloque o sesión (D-20).

## 3. Ciclo de vida de una app (D-28)

| Fase | Pregunta | Quién | Resultado (en `semillas/<App>/`) | Cierre |
|---|---|---|---|---|
| 1. Idea | ¿Qué NO quiero y qué más o menos SÍ? | Javier responde; Claude pregunta de a una | `IDEA.md` | Al menos un "no quiero" y un "sí quiero", confirmado por Javier |
| 2. Descubrimiento | ¿Qué pasa de verdad con documentos reales? | Claude prueba `C:\JV\pruebas`; Javier aclara | `DESCUBRIMIENTO.md`: historia de uso, catálogo de casos difíciles, puntos calientes | Cada documento de prueba clasificado y cada caso difícil con una decisión |
| 3. Prototipo | ¿Así se ve y así se usa, completa? | Claude dibuja bocetos de la **versión final** (todos los juegos), marcando en qué juego llega cada elemento; Javier aprueba. Después OpenCode los construye en WPF con datos de ejemplo y Claude revisa con capturas (D-51) | `PROTOTIPO.md` + capturas | Javier aprueba cada pantalla |
| 4. Especificación | ¿Qué debe hacer exactamente? | Claude propone; Javier aprueba | `SPEC.md` + `decisiones/` | Lista de "listo para construir" (abajo) |
| 5. Construcción por juegos | Apertura → medio → final | OpenCode ejecuta bloques; Claude revisa | código + `bloques/` | Cada juego termina en un hito que Javier prueba |
| 6. Uso real | ¿Aguanta el trabajo diario? | Javier usa; los hallazgos se registran como Casos | `casos/Caso-N.md` | Un mes de uso sin cambios (D-16, D-17) |

**Juegos** (D-28): la **apertura** es el recorrido mínimo de punta a punta que Javier puede usar en su trabajo; el **medio** suma las reglas de negocio; el **final** pule con uso real.

**Especificación lista para construir** (sección 4 de `SPEC.md`):
- [ ] Cada requisito tiene al menos un Given/When/Then, propuesto por la IA y aprobado por Javier (D-09). Cada uno se convierte en prueba automática.
- [ ] Cada requisito no funcional tiene una medida numérica (atributo, estímulo, entorno, respuesta y medida).
- [ ] La sección "Qué NO construir" no está vacía.
- [ ] Una vuelta completa de "¿Qué tal si...?" no encontró nada nuevo. Preguntas obligatorias: ¿y si falla a la mitad?, ¿y si dos cosas pasan a la vez?, ¿y si el orden es distinto?, ¿y si el dato está vacío, dañado o no existe?, ¿y si no hay red?, ¿y si el usuario quiere cambiarlo después de configurarlo?
- [ ] Toda decisión con ventajas y desventajas reales tiene su ADR en `decisiones/`.
- [ ] Ningún nombre visible para el usuario quedó elegido por la IA.

**Cambios sobre una app que ya existe:** un Caso (`casos/Caso-N.md`) describe solo lo que se agrega, cambia o quita (delta); al aprobarse, se actualiza `SPEC.md` y se crean los bloques. Nunca se corrige el código sin pasar antes por el Caso.

## 4. Orden de desarrollo (D-25, D-30)

1. Se arma el mapa de dependencias: qué piezas usa cada app.
2. Se construye primero lo que no depende de nada; después lo que se apoya en lo ya construido.
3. De cada pieza compartida se construye solo lo que pide la app de encima.
4. Primero las aperturas de todas las apps, en orden de dependencias; después cada app se completa en el mismo orden (D-33).
5. Mientras una app cumple su mes de uso se avanza con la siguiente; un arreglo interrumpe y reinicia ese mes (D-17).

## 5. Trabajo de los agentes

### 5.1 El bloque

Unidad mínima de trabajo. Una nota en `bloques/B-NNN.md` (plantilla `plantillas/BLOQUE.md`) con propiedades que Obsidian muestra en el tablero y la IA lee como texto estructurado (D-34).

- Tamaño: **como máximo 3 archivos de código (.cs, .xaml) por corrida de OpenCode**; los de configuración del proyecto (.csproj, .slnx, .props) no cuentan. Un bloque con 8 plantillas (B-001) agotó la respuesta del modelo sin escribir nada (`finish: length`); partido en corridas de 2 o 3 archivos, funcionó. Si un bloque necesita más, se divide.
- Debe decir: objetivo en una frase, requisitos de `SPEC.md` que cubre, archivos permitidos, archivos prohibidos, criterio de término (pruebas que deben pasar) y casos límite explícitos. Un bloque sin casos límite no se entrega (lección de B-000).
- Estados: `pendiente` → `en curso` → `en revisión` → `aprobado` | `devuelto` | `bloqueado`.

### 5.2 Ciclo de un bloque (D-44)

1. Claude escribe el bloque y lo deja `pendiente`.
2. Claude crea la rama `bloque/B-NNN` y lanza OpenCode con el modelo elegido (5.3).
3. OpenCode implementa, corre las pruebas y deja su reporte en la nota del bloque.
4. Claude revisa: diferencias contra la rama principal, pruebas, formato (CSharpier), código de más (`/ponytail-review`), licencias, secretos (gitleaks) y, si hay pantalla, capturas automáticas (FlaUI).
5. Si está bien: `aprobado`, Claude integra la rama a `main` y sube a GitHub. Javier no revisa bloque por bloque (D-13).
6. Si no: `devuelto` con las correcciones exactas. Al tercer intento fallido con el mismo error, el bloque pasa a Claude.
7. Al completar un hito, Claude avisa a Javier para que pruebe la app real.

### 5.3 Qué modelo usa OpenCode (D-45)

| Tipo de bloque | Modelo |
|---|---|
| Rutina (pruebas, textos, formato, pantallas con patrón conocido) | `glm-5.3-flash` |
| Normal | `glm-5.3` |
| Difícil | `kimi-k3` (cuota escasa) |

DeepSeek no se usa mientras la cuenta no tenga activadas las regiones globales (hallazgo del 2026-10-03). La lista de modelos rota: se revisa con `opencode models opencode-go`.

### 5.4 Qué hace Claude directamente

Arquitectura y núcleo compartido, modelo de datos, seguridad, lo que falló tres veces en OpenCode, especificación y revisión.

### 5.5 Reglas de parada (para cualquier agente)

Se detiene y escribe el traspaso cuando: cumplió el criterio de término; necesita tocar un archivo fuera de su alcance; falló tres veces con el mismo error; aparece una decisión de arquitectura, de alcance o un nombre; las pruebas fallan por algo ajeno al bloque; o se acerca al límite de uso del plan. Nunca deja código sin commit ni sigue "un poco más" fuera del bloque.

### 5.6 Reglas de reinicio

Al empezar una sesión: leer `AGENTS.md`, la última entrada de `ESTADO.md`, `git status` y el último commit; correr las pruebas; tomar el siguiente bloque del tablero. Si algo no coincide con lo anotado, detenerse e informar antes de editar.

### 5.7 Datos

Nunca se pegan documentos reales de clientes en una IA (D-11). El código es público, así que cualquier modelo puede leerlo.

## 6. La semilla de una app (D-12)

`semillas/<App>/` contiene todo lo necesario para construir la app sin otra conversación:

| Archivo | Fase |
|---|---|
| `IDEA.md` | 1 |
| `DESCUBRIMIENTO.md` | 2 |
| `PROTOTIPO.md` + `capturas/` | 3 |
| `SPEC.md` | 4 |
| `decisiones/ADR-NNN-*.md` | 3 y 4 |
| `casos/Caso-N.md` | 5 y 6 |

Plantillas en `C:\JV\MQD\plantillas\`.

## 7. Diseño (D-35, D-36)

- Estilo minimalista propio, común a toda la familia del ecosistema.
- Pantallas amplias y simples: pocas cosas por pantalla, botones grandes, textos que explican qué hace cada cosa.
- Modo claro u oscuro según Windows, con opción de cambiarlo en la app.
- Colores de Hormiguero: modo claro, principal grafito azul `#2E5C8A` con acento verde hoja; modo oscuro, principal verde hoja `#6CC08B` con acento grafito azul `#7FB0E0`.
- El sistema de diseño (colores, tamaños, tipografía, controles) vive en el núcleo compartido y se define antes de la primera pantalla.

## 8. Integración (D-18)

- Toda app funciona sola; si encuentra a las otras instaladas, aprovecha sus datos.
- El código común vive en un núcleo compartido dentro del monorepo, nunca copiado.
- El mecanismo para compartir datos se decide con un ADR antes de construir el núcleo (pendiente).

## 9. Reglas técnicas heredadas (huecos conocidos de la v1)

1. Toda app empieza con un paso de primera configuración.
2. Mover o renombrar un archivo = copiar, verificar que la copia es idéntica y recién ahí borrar el original.
3. Todo dato extraído de un documento se valida; si no tiene forma razonable, va a revisión manual.
4. Nunca se vigila una carpeta de red con consultas agresivas: se revisa al iniciar o a pedido ("vecino silencioso").
5. Una app que reacciona a archivos revisa al iniciar lo que llegó mientras estaba cerrada.
6. Borrar una plantilla nunca borra lo creado con ella.
7. Una acción irreversible que destruye trabajo manual exige una confirmación con espera.
8. Nunca se modifica un documento original: las marcas viven aparte.
9. Toda dependencia debe tener licencia compatible con la del ecosistema (D-27).

## 10. Cuándo algo está completo (D-16)

Javier lo usó en su trabajo real al menos un mes sin hacerle cambios, y funciona tanto solo como integrado con el resto del ecosistema.

## 11. Pendiente de este documento

- (Resuelto: secciones [PROPUESTA] aprobadas como D-44 a D-46.)
- ADR de datos comunes (sección 8).
- Skill propia de interrogatorio (adaptación de grill-me: una pregunta a la vez, sin subagentes).
- Plantilla de requisitos no funcionales: unificar "6 o 7 campos" (pendiente heredado de la v1).
