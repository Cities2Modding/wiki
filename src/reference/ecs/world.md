# World

The ECS World is represented by `Unity.Entities.World`.

A World contains basically all the [Entities](./entity.md) in the game and has the relevant [Systems](./system.md) attached to it. Systems can only access Entities belonging to the same World as themselves.