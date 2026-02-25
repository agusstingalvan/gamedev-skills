# Mapeo de patrones a Phaser

## Creacionales

- Factory Method: crear enemigos/proyectiles por tipo sin `if/switch` dispersos.
- Abstract Factory: familias de contenido por biome/tema (hielo, bosque, lava).
- Builder: construir niveles por pasos (tilemap, layers, colision, spawn).
- Prototype: clonar configuraciones base de enemigos o armas.
- Singleton: servicios globales (audio, configuracion, telemetria) con acceso controlado.

## Estructurales

- Adapter: adaptar plugins o SDKs externos al contrato interno del juego.
- Bridge: separar logica de alto nivel (arma) de implementacion (hitscan/proyectil).
- Composite: manejar HUD o arboles de nodos con interfaz uniforme.
- Decorator: agregar buffs/debuffs/efectos sin tocar clase base.
- Facade: API unica para subsistemas (save, audio, UI feedback, analytics).
- Flyweight: compartir estado intrinseco en objetos masivos + object pooling.
- Proxy: cache, lazy load o control de acceso a servicios remotos.

## Comportamiento

- Chain of Responsibility: pipeline de input o validaciones.
- Command: encapsular acciones del jugador para replay/undo/rebind.
- Iterator: recorridos especializados sobre colecciones activas.
- Mediator: coordinar componentes sin dependencia directa entre ellos.
- Memento: checkpoints y rollback de estado.
- Observer: eventos de gameplay desacoplados (`this.events`, EventEmitter).
- State: IA o control con estados explicitos (Idle, Patrol, Chase, Attack).
- Strategy: intercambiar algoritmos en runtime (movimiento, targeting, disparo).
- Template Method: flujo base de escena con pasos sobreescribibles.
- Visitor: operaciones nuevas sobre jerarquias estables de entidades.

## Heuristica de eleccion

- Si cambia **que se crea**, usar patron creacional.
- Si cambia **como se conectan piezas**, usar patron estructural.
- Si cambia **como actuan en runtime**, usar patron de comportamiento.
