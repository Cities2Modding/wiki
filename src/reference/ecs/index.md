# ECS

For a beginners introduction to ECS and how Cities: Skylines 2 uses ECS, please check out ["Guide - ECS"](../../guides/ecs.md) that goes into more depth.

- [Entity](./entity.md)
- [Component](./component.md)
- [System](./system.md)
- [Query](./query.md)
- [World](./world.md)
- [Phase](./phase.md)
- [Archetype](./archetype.md)

Basic architecture:

One [Entity](./entity.md), Has one or more [Components](./component.md).

[Systems](./system.md) run independently and are part of the Game Loop. Systems usually [Query](./query.md) for [Components](./component.md), then act based on the data they retrieve, possibly mutates data inside the [Components](./component.md) too.