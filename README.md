# Robotic Control & Sensor Mapping Project

This project provides a **console-based** application for controlling a robot, managing sensors (e.g., IR and Lidar), and performing environment mapping. It is designed in a **modular, object-oriented** fashion, allowing easy extension for new robot platforms, sensors, or additional features.

## 1. Project Overview

- **Goal**: Offer a text-driven, menu-based interface for controlling a robot (connect/disconnect, move, rotate, stop) and retrieving sensor data (IR, Lidar), then mapping or handling safe navigation.
- **Architecture**:  
  - **App**: Main application flow.  
  - **Menus** (Connection, Motion, Sensor): Handle user interactions and direct commands.  
  - **RobotController**: Central unit for movements, sensor updates, access control (password).  
  - **RobotInterface**: Abstract interface describing how to connect, move, rotate, stop the robot, and manage sensors.  
  - **FestoRobotAPI**: Low-level library or hardware API that directly communicates with the robot.  
  - **IRSensor, LidarSensor**: Retrieve sensor data from the `FestoRobotAPI`.  
  - **Map, Mapper**: Store and process environment data (e.g., from Lidar).  
  - **Authentication**: User login and password verification.  
  - **Record**: File I/O (logging, map saving).

## 2. Robot Communication

This system communicates with the actual robot using:
- **FestoRobotAPI**: A library (or driver) that provides low-level methods such as:
  - `connect()`, `disconnect()`
  - `move(direction)`, `rotate(direction)`, `stop()`
  - `getIRRange(index)`, `getLidarRange(...)`
  - `getXYTh(...)` for pose

Internally, the **`FestoRobotInterface`** (a subclass of `RobotInterface`) calls these FestoRobotAPI methods. For example:
- `FestoRobotInterface::connect()` → calls `api->connect()`
- `FestoRobotInterface::move(FORWARD)` → calls `api->move(FORWARD)`

> **Protocol**: Typically, the FestoRobotAPI might be using a custom or proprietary protocol over some interface (TCP, serial, etc.). In this project, we treat it as a **library** providing direct function calls. If you have a real device, check the Festo or hardware documentation for the underlying transport layer.

## 3. How the Robot is Controlled

1. **Startup**:  
   - The user runs the console application, which initiates `App::run()`.  
   - **Authentication** ensures correct access code is provided.

2. **Main Menu & Submenus**:  
   - **ConnectionMenu**: The user can connect or disconnect the robot using `RobotController::connectRobot()` or `disconnectRobot()`.  
   - **MotionMenu**: The user can move forward/backward, turn left/right, or stop. These calls invoke `RobotController` methods, which in turn invoke `RobotInterface` methods.  
   - **SensorMenu**: The user can view IR or Lidar data; the system calls `updateSensors()`, which loops over all sensors in the `RobotInterface`’s sensor list.

3. **Access Control**:  
   - `RobotController` provides `openAccess(password)` and `closeAccess()`. If access is locked, movement commands do nothing.

4. **Mapping & Safe Navigation**:  
   - Optionally, `Mapper` processes Lidar data to mark obstacles on a grid.  
   - `SafeNavigation` uses IR sensor data to stop the robot if an obstacle is too close.

## 4. Setup & Usage

1. **Build**  
   - Ensure you have a C++ compiler and any dependencies (e.g. the real or simulated `FestoRobotAPI`).  
   - Compile all `*.cpp` files together. For instance:
     ```bash
     g++ -std=c++11 -Iinclude -o robot_app *.cpp
     ```
   - Adjust include paths/names as needed.

2. **Running**  
   - Run the executable `robot_app`.  
   - On startup, you will be asked for **name**, **surname**, and an **access code**.  
   - If authentication is correct, the main menu is shown.

3. **Sample Flow**:  
   - **Connection**: “Connect Robot” → `RobotController::connectRobot()` → `FestoRobotAPI::connect()`.  
   - **Movement**: “Move Forward” → calls `RobotController::moveForward()` → eventually `FestoRobotAPI::move(FORWARD)`.  
   - **Sensors**: “Update All Sensors” → loops over IR / Lidar, calls `updateSensor()`.  
   - **Mapping** (if invoked): collects Lidar data as `(distance, angle)` pairs, uses trigonometry to mark cells in `Map`.

## 5. Potential Project Extensions

- **Additional RobotPlatforms**: By creating new subclasses of `RobotInterface`, you can integrate different hardware or simulators.  
- **More Sensors**: Subclasses of `SensorInterface` (e.g., ultrasonic, camera-based) can be added.  
- **GUI or Web Interface**: Replace the console-based menu with a graphical or web-based front-end, reusing the core classes.

## 6. Contributing & License

- **Contributions**: Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.  
- **License**: This project is distributed under [Your License of Choice]—feel free to adapt it to your needs.

---

**Enjoy controlling your robot** using this modular, object-oriented framework. If you have any questions or issues, please contact [project maintainer / email / GitHub link].
