# Discrete Parallel Transport: A Framework for Non-Euclidean Game Worlds

## Project Overview

This post documents my implementation of non-Euclidean geometry in a cooperative game environment. The project uses concepts from differential geometry, particularly parallel transport, to create a general framework for representing various non-Euclidean spaces in a grid-based game world.

## Parallel Transport Implementation

The core mathematical concept I implemented is parallel transport, which describes how vectors change when moved along paths in curved spaces. In the context of my game, this determines how a player's orientation changes when moving through different regions of space.

The implementation is centered around the `ParallelTransporter` class:

```cpp
class ParallelTransporter {
public:
    // Conversion between Direction enum and integer representations
    static int Direction2Int(Direction xDir);
    static Direction Int2Direction(int x);
    
    // Functions that implement different rotations
    static int TurnLeft(int x);
    static int TurnRight(int x);
    static int Turn180(int x);
    static int StayStraight(int x);
    
    // Calculates resulting direction after movement
    Direction CalculateDireciton(Direction facingWhere, Direction goingWhere);
    
    // Map of rotation functions
    Int2RotationOnInt directionInt2RotationInt;
};
```

This class encodes how directions transform when moving through space. For each grid cell, the `directionInt2RotationInt` map defines what happens to a player's orientation when moving in each of the four cardinal directions. This allows for precise control over the local geometry at each point in the game world.

## Movement System

The movement system is implemented in the `MovementManager` class, which coordinates how entities navigate the non-Euclidean space:

```cpp
class MovementManager {
public:
    std::vector<std::vector<Action2Coord2d*>> grid2Transporter;
    std::vector<std::vector<ParallelTransporter*>> grid2ParallelTransporter;
    
    NavigationInfo Move(Coord2d position, Direction movingDirection, Direction facingDirection);
};
```

The `Move` method calculates both the new position and new orientation when moving in a particular direction. It returns a `NavigationInfo` struct containing the updated position, direction, and orientation change.

## Cube World Implementation

As a concrete example, I implemented a cube world where a flat grid wraps around the six faces of a cube. This requires careful handling of the connections between faces, particularly at the edges:

```cpp
void MovementManager::InitTransporters(int startY, int startX, int size) {
    // Code that sets up connections between cube faces
    // Defines how direction changes when crossing between faces
}
```

When a player moves across an edge of the cube, both their position and orientation update according to the parallel transport rules. For example, moving "up" from the front face of the cube places the player on the top face, but now "up" means a different direction in the global coordinate system.

<video width="100%" controls>
   <source src="/assets/videos/cube_demo.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video>

## Visual Representation

To visualize the non-Euclidean space, I implemented the `GridTransformManager` class, which defines 3D positions for each grid cell:

```cpp
void GridTransformManager::InitTransforms(int startY, int startX, int size) {
    // Maps logical grid positions to 3D coordinates and orientations
}
```

This creates a coherent visual representation where the connections defined by parallel transport are reflected in the game's graphics.

## Extensibility

The framework is designed to be general, allowing for various non-Euclidean spaces to be implemented:

1. **Torus worlds**: The `InitializeTori` method sets up a simple wrap-around world
<video width="100%" controls>
   <source src="/assets/videos/torus_demo.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video>
2. **Custom manifolds**: By defining appropriate parallel transport rules, any manifold can be represented

## Technical Details

The implementation uses several key data structures:

- `Action2Coord2d`: Maps a movement direction to the resulting coordinate
- `Int2RotationOnInt`: Maps direction integers to rotation functions
- `Coord2dWithDirection`: Represents position and orientation

The system discretizes continuous geometric concepts to work in a grid-based environment, but preserves the essential mathematical properties of parallel transport.

## Applications

This technical framework enables several gameplay possibilities:

1. Spatial puzzles that leverage non-Euclidean geometry
2. Environments that are more complex than their memory footprint would suggest
3. Navigation challenges where understanding the geometry is key

---
Want to see the full code or learn more? Check out the GitHub repository: 

1. [Mandelbrot-Planet](https://github.com/RubADuckDuck/Mandelbrot-Planet) 
2. https://github.com/RubADuckDuck/Mandelbrot-Planet/blob/main/include/Core/GridManager.h
3. https://github.com/RubADuckDuck/Mandelbrot-Planet/blob/main/src/Core/GridManager.cpp