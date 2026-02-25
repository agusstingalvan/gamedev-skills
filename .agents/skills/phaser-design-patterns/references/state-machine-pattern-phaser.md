# EnemyExample - State Machine Pattern (Phaser 3 + JavaScript)

Implementacion completa del patron State Machine adaptada a Phaser.

## Configuracion

- Clase principal: `EnemyExample`
- Patron: State + State Machine
- Motor: Phaser 3 (JavaScript)
- Incluye controlador/contexto: si
- Estados: `Idle`, `Move`, `Action`
- Callbacks de estado:
  - `onSensorOverlap(target)` (equivalente a `OnTriggerEnter`)
  - `onCustomEvent(eventData)` (equivalente a `MetodoCustom`)

## Arquitectura

- `EnemyExampleState`: contrato base para todos los estados.
- `EnemyExampleStateMachine`: mantiene estado actual y transiciones.
- `EnemyExample`: contexto del enemigo + puente con Phaser (update, overlap, eventos).
- `EnemyExampleIdleState`, `EnemyExampleMoveState`, `EnemyExampleActionState`: estados concretos.

---

## `EnemyExampleState.js`

```js
export class EnemyExampleState {
  enter(ctx) {}
  update(ctx, time, delta) {}
  exit(ctx) {}
  onSensorOverlap(ctx, target) {}
  onCustomEvent(ctx, eventData) {}
}
```

## `EnemyExampleStateMachine.js`

```js
export class EnemyExampleStateMachine {
  constructor(enemy) {
    this.enemy = enemy;
    this.currentState = null;

    this.idleState = null;
    this.moveState = null;
    this.actionState = null;
  }

  registerStates({ idleState, moveState, actionState }) {
    this.idleState = idleState;
    this.moveState = moveState;
    this.actionState = actionState;
  }

  initialize(startingState) {
    this.currentState = startingState;
    this.currentState.enter(this.enemy);
  }

  transitionTo(nextState) {
    if (!nextState || this.currentState === nextState) return;
    if (this.currentState) this.currentState.exit(this.enemy);
    this.currentState = nextState;
    this.currentState.enter(this.enemy);
  }

  update(time, delta) {
    if (this.currentState) this.currentState.update(this.enemy, time, delta);
  }

  onSensorOverlap(target) {
    if (this.currentState) this.currentState.onSensorOverlap(this.enemy, target);
  }

  onCustomEvent(eventData) {
    if (this.currentState) this.currentState.onCustomEvent(this.enemy, eventData);
  }
}
```

## `EnemyExample.js` (Contexto)

```js
import { EnemyExampleStateMachine } from './EnemyExampleStateMachine.js';
import { EnemyExampleIdleState } from './EnemyExampleIdleState.js';
import { EnemyExampleMoveState } from './EnemyExampleMoveState.js';
import { EnemyExampleActionState } from './EnemyExampleActionState.js';

export class EnemyExample extends Phaser.Physics.Arcade.Sprite {
  constructor(scene, x, y, texture = 'enemy') {
    super(scene, x, y, texture);
    scene.add.existing(this);
    scene.physics.add.existing(this);

    this.scene = scene;
    this.speed = 80;
    this.visionRange = 180;
    this.attackRange = 45;
    this.hp = 100;
    this.target = null;

    this.stateMachine = new EnemyExampleStateMachine(this);

    this.idleState = new EnemyExampleIdleState();
    this.moveState = new EnemyExampleMoveState();
    this.actionState = new EnemyExampleActionState();

    this.stateMachine.registerStates({
      idleState: this.idleState,
      moveState: this.moveState,
      actionState: this.actionState
    });

    this.stateMachine.initialize(this.idleState);

    // Ejemplo de callback equivalente a OnTriggerEnter:
    // playerGroup debe existir en la escena.
    if (scene.playerGroup) {
      scene.physics.add.overlap(this, scene.playerGroup, (_enemy, player) => {
        this.onSensorOverlap(player);
      });
    }
  }

  preUpdate(time, delta) {
    super.preUpdate(time, delta);
    this.stateMachine.update(time, delta);
  }

  onSensorOverlap(target) {
    this.stateMachine.onSensorOverlap(target);
  }

  onCustomEvent(eventData) {
    this.stateMachine.onCustomEvent(eventData);
  }

  setTarget(player) {
    this.target = player;
  }

  distanceToTarget() {
    if (!this.target) return Number.POSITIVE_INFINITY;
    return Phaser.Math.Distance.Between(this.x, this.y, this.target.x, this.target.y);
  }

  stopMotion() {
    this.setVelocity(0, 0);
  }

  moveToTarget() {
    if (!this.target) return;
    const angle = Phaser.Math.Angle.Between(this.x, this.y, this.target.x, this.target.y);
    this.setVelocity(Math.cos(angle) * this.speed, Math.sin(angle) * this.speed);
  }

  attackTarget() {
    if (!this.target) return;
    this.scene.events.emit('enemy-attack', {
      enemyId: this.name || 'enemy-example',
      targetId: this.target.name || 'player',
      damage: 10
    });
  }
}
```

## `EnemyExampleIdleState.js`

```js
import { EnemyExampleState } from './EnemyExampleState.js';

export class EnemyExampleIdleState extends EnemyExampleState {
  enter(ctx) {
    ctx.stopMotion();
    ctx.setTint(0x9e9e9e);
  }

  update(ctx) {
    if (!ctx.target) return;
    const distance = ctx.distanceToTarget();
    if (distance <= ctx.visionRange) {
      ctx.stateMachine.transitionTo(ctx.moveState);
    }
  }

  exit(ctx) {
    ctx.clearTint();
  }

  onSensorOverlap(ctx, target) {
    ctx.setTarget(target);
    ctx.stateMachine.transitionTo(ctx.actionState);
  }

  onCustomEvent(ctx, eventData) {
    if (eventData?.type === 'ALERT') {
      ctx.stateMachine.transitionTo(ctx.moveState);
    }
  }
}
```

## `EnemyExampleMoveState.js`

```js
import { EnemyExampleState } from './EnemyExampleState.js';

export class EnemyExampleMoveState extends EnemyExampleState {
  enter(ctx) {
    ctx.setTint(0x64b5f6);
  }

  update(ctx) {
    if (!ctx.target) {
      ctx.stateMachine.transitionTo(ctx.idleState);
      return;
    }

    const distance = ctx.distanceToTarget();
    if (distance > ctx.visionRange * 1.25) {
      ctx.stateMachine.transitionTo(ctx.idleState);
      return;
    }

    if (distance <= ctx.attackRange) {
      ctx.stateMachine.transitionTo(ctx.actionState);
      return;
    }

    ctx.moveToTarget();
  }

  exit(ctx) {
    ctx.stopMotion();
    ctx.clearTint();
  }

  onSensorOverlap(ctx) {
    ctx.stateMachine.transitionTo(ctx.actionState);
  }
}
```

## `EnemyExampleActionState.js`

```js
import { EnemyExampleState } from './EnemyExampleState.js';

export class EnemyExampleActionState extends EnemyExampleState {
  enter(ctx) {
    ctx.stopMotion();
    ctx.setTint(0xef5350);
    ctx.attackCooldownMs = 600;
    ctx.nextAttackAt = 0;
  }

  update(ctx, time) {
    if (!ctx.target) {
      ctx.stateMachine.transitionTo(ctx.idleState);
      return;
    }

    const distance = ctx.distanceToTarget();
    if (distance > ctx.attackRange) {
      ctx.stateMachine.transitionTo(ctx.moveState);
      return;
    }

    if (time >= ctx.nextAttackAt) {
      ctx.attackTarget();
      ctx.nextAttackAt = time + ctx.attackCooldownMs;
    }
  }

  exit(ctx) {
    ctx.clearTint();
  }

  onCustomEvent(ctx, eventData) {
    if (eventData?.type === 'STUN') {
      ctx.stateMachine.transitionTo(ctx.idleState);
    }
  }
}
```

---

## Uso rapido en una Scene Phaser

```js
import { EnemyExample } from './EnemyExample.js';

class GameScene extends Phaser.Scene {
  create() {
    this.player = this.physics.add.sprite(300, 220, 'player');
    this.playerGroup = this.physics.add.group([this.player]);

    this.enemy = new EnemyExample(this, 120, 220, 'enemy');
    this.enemy.setTarget(this.player);

    this.input.keyboard.on('keydown-A', () => {
      this.enemy.onCustomEvent({ type: 'ALERT' });
    });

    this.input.keyboard.on('keydown-S', () => {
      this.enemy.onCustomEvent({ type: 'STUN' });
    });
  }
}
```

## Flujo de transicion

1. Llamar `stateMachine.transitionTo(nextState)`.
2. Ejecutar `currentState.exit(ctx)`.
3. Actualizar `currentState`.
4. Ejecutar `nextState.enter(ctx)`.
5. En cada frame, ejecutar `currentState.update(ctx, time, delta)`.

## Notas practicas para Phaser

- Usar `preUpdate(time, delta)` en sprites personalizados para conectar la state machine al loop.
- En callbacks de `overlap/collider`, delegar a `stateMachine.onSensorOverlap(...)`.
- Mantener una sola transicion por frame para evitar estados inconsistentes.
- Si un estado se suscribe a eventos globales, limpiar en `exit()`.
