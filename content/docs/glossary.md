---
title: Terminology and Glossary
---

What specific terms, words and phrases mean in the context of Causality

## A
### Actor
## G
### Game Loop
All the calls and processing that happens in a singular frame of the game.
## S
### Shadow Splits
To compute shadow maps, the scene is rendered (only depth) from an orthogonal point of view that covers the whole scene (or up to the max distance). There is, however, a problem with this approach because objects closer to the camera receive low-resolution shadows that may appear blocky. To fix this, a technique named Parallel Split Shadow Maps (PSSM) is used. This splits the view frustum in 2 or 4 areas. Each area gets its own shadow map. The distances at which these splits are made are called **shadow splits**.

## References
Some text belongs to Juan Linietsky, Ariel Manzur and the Godot community (Licensed under CC BY 3.0)