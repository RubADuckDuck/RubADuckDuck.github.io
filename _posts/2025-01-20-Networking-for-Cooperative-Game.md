# Building Resilient Client-Server Game Communication

github links:  
1. [Mandelbrot-Planet](https://github.com/RubADuckDuck/Mandelbrot-Planet) 
2. <https://github.com/RubADuckDuck/Mandelbrot-Planet/tree/main/src/Network>
3. <https://github.com/RubADuckDuck/Mandelbrot-Planet/tree/main/include/Network>

## Project Overview

This project implements a networked cooperative game with a focus on robust client-server communication and game state management.

## Technical Architecture

The game uses a dual-protocol networking approach:
- TCP for reliable, ordered communication
- UDP for real-time, low-latency game updates

### Key Components

1. **Network Message System**
   The system translates network messages into game commands through a multi-step process:
   - Network messages represent raw game events
   - These messages are converted to game commands
   - Commands modify the game state

   Example of message processing:
   ```cpp
   std::unique_ptr<IGameCommand> GameMessageProcessor::ProcessMessage(const INetworkMessage& message) {
       switch (message.GetType()) {
       case MessageType::PLAYER_INPUT:
           return std::make_unique<PlayerInputCommand>(
               message.playerDirection, 
               message.playerID
           );
       // Other message types handled similarly
       }
   }
   ```

2. **Game State Management**
   The `GameState` class manages the entire game world, including:
   - Object creation and registration
   - Dynamic object type handling
   - Grid-based positioning
   - Object lifecycle management

3. **Connection Workflow**
   The connection process involves several stages:
   - TCP handshake
   - Authentication
   - UDP verification
   - Game state synchronization

### Technical Implementations

#### Object Creation
```cpp
void GameState::CreateAndRegisterGameObjectWithID(uint32_t id, uint8_t typeId, bool fromNetwork) {
    std::unique_ptr<GameObject> newGameObject;
    newGameObject = factoryRegistry[typeId]->Create();
    newGameObject->SetID(id);
    gameObjects[id] = std::move(newGameObject);
    objectsByType.insert({ typeId, id });
}
```

#### Message Handling
```cpp
void NetworkCodec::HandleNetworkData(const std::vector<uint8_t>& data, GameState& gameState) {
    try {
        std::unique_ptr<INetworkMessage> message = Decode(data);
        std::unique_ptr<IGameCommand> command = GameMessageProcessor::GetInstance().ProcessMessage(*message);
        command->Execute(gameState);
    }
    catch (const std::exception& e) {
        std::cerr << "Error processing message: " << e.what() << std::endl;
    }
} 
```

## Technical Challenges Addressed

- Consistent game state synchronization
- Secure connection verification
- Real-time input handling
- Flexible object management

## Technologies and Tools

- C++
- ASIO Networking Library
- UDP/TCP Protocols
- Object-Oriented Design Patterns

## Implementation Highlights

- Secure authentication mechanism
- Cryptographically secure verification codes
- Dynamic object creation and management
- Flexible communication protocol
- Efficient state synchronization

Want to see the full code or learn more? Check out the GitHub repository: 

1. [Mandelbrot-Planet](https://github.com/RubADuckDuck/Mandelbrot-Planet) 
2. <https://github.com/RubADuckDuck/Mandelbrot-Planet/tree/main/src/Network>
3. <https://github.com/RubADuckDuck/Mandelbrot-Planet/tree/main/include/Network>
