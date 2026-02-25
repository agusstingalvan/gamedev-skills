---
name: phaser-design-patterns
description: Implementacion de patrones de diseno en Phaser 3 con JavaScript, orientada a arquitectura de escenas, entidades, input, fisicas y rendimiento. Usar cuando pidan aplicar, refactorizar, revisar o ensenar patrones de diseno (creacionales, estructurales o de comportamiento) dentro de juegos Phaser.
---

# Phaser Design Patterns

Implementar patrones de diseno con foco en gameplay real, no en ejemplos abstractos.

## Flujo de trabajo

1. Identificar problema real del juego (creacion de entidades, coordinacion entre sistemas, variacion de comportamiento, etc).
2. Elegir un patron que reduzca acoplamiento y no agregue complejidad innecesaria.
3. Mapear el patron a primitivas Phaser (Scene, GameObject, Group, Events, Physics, Time, Input).
4. Implementar en JavaScript con nombres de dominio del juego.
5. Validar en runtime (escena carga, input responde, colisiones funcionan, no hay leaks de eventos).

## Seleccion rapida de patron

- Creacion variable de entidades -> Factory Method / Abstract Factory.
- Construccion por pasos de nivel o UI -> Builder.
- Estado compartido para miles de instancias -> Flyweight + pooling.
- Reglas por modo IA/comportamiento -> State / Strategy.
- Eventos desacoplados entre gameplay y UI -> Observer / Mediator.
- Acciones re-ejecutables (input, replay, undo) -> Command.

## Reglas de implementacion

- Priorizar composicion sobre herencia profunda.
- Mantener escenas pequenas y con responsabilidades claras.
- Evitar singletons mutables para logica de gameplay; reservarlos para servicios globales (audio, config).
- Usar `delta` en `update(time, delta)` cuando el patron afecte movimiento o timers.
- Desuscribir eventos en `shutdown`/`destroy` para evitar memory leaks.
- Para objetos masivos (balas, particulas), combinar patron con pooling (`setActive(false)`, `setVisible(false)`).

## Entregables esperados

- Explicar en 2-4 lineas por que el patron resuelve el problema.
- Mostrar snippet en JavaScript/Phaser con contexto real del juego.
- Incluir riesgos y anti-patrones relevantes.
- Proponer validacion minima (prueba manual o test simple).

## Referencias

- Ver `references/patterns-map.md` para mapeo completo patron -> uso en Phaser.
- Ver `references/phaser-snippets.md` para plantillas listas para copiar y adaptar.
- Ver `references/state-machine-pattern-phaser.md` para implementacion completa de State + StateMachine (Idle/Move/Action) adaptada a Phaser.
