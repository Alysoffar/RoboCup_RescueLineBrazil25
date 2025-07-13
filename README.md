# RoboCupJunior Rescue Line - Spike Prime Robot Code

This repository contains the LEGO Spike Prime block-based code for "Prime-1," a robot designed for the RoboCupJunior Rescue Line competition.

## 1. Overview

The program enables the robot to autonomously navigate a black line on a white field, handle special course elements like intersections, and perform a victim rescue in a designated evacuation zone.

The software architecture is event-driven. A main `LineFollower` loop runs continuously, while events (like detecting a specific color) are handled by separate, broadcast-triggered functions. This allows for clean, parallel execution of tasks.

## 2. Hardware Requirements

The code is designed for a specific hardware configuration. All components must be connected to the correct ports on the SPIKE Prime Hub for the program to function.

*   **Main Controller:** 1x LEGO SPIKE Prime Hub
*   **Drive Motors:**
    *   Left Motor: **Port A** (Large Motor)
    *   Right Motor: **Port B** (Large Motor)
    *   _Note: The program uses the `move` block, which assumes motors are on A & B by default._
*   **Manipulator Motor:**
    *   Forklift Arm Motor: **Port F** (Medium or Large Motor)
*   **Sensors:**
    *   Left Line Follower: **Port C** (Color Sensor)
    *   Right Line Follower: **Port D** (Color Sensor)
    *   Environmental Sensor: **Port E** (Color Sensor, forward-facing)

## 3. Software Breakdown

The code is organized into custom functions (My Blocks) and event handlers.

### `when program starts`
This is the main entry point. It simply calls the `LineFollower` function, starting the robot's main operational loop.

### `LineFollower` (Custom Block)
This is the core of the robot's navigation logic.
*   **Proportional Control:** It implements a P-Controller for smooth line following.
    *   It calculates an `error` by subtracting the reflected light value of the Right Sensor (D) from the Left Sensor (C). `Turn = (Sensor C % - Sensor D %)`
    *   This `error` value is multiplied by a proportional gain constant (`Kp = 1.5`).
    *   The result is fed directly into the steering input of a `move` block, making the robot turn proportionally to its distance from the line's center.
*   **Event Detection:** Inside its main loop, it continuously checks the Environmental Sensor (E) for two conditions:
    *   If **green** is detected, it broadcasts the "green" message.
    *   If **reflected light is less than 20%** (indicating the silver reflective tape of the evac zone), it broadcasts the "evac" message.

### `green` (Custom Block & `when I receive green`)
This block handles intersections marked by a green tile.
1.  Stops all movement.
2.  Moves forward a set distance to clear the green square.
3.  Executes a timed left turn (0.28 seconds) to navigate the intersection.
4.  The main `LineFollower` loop then resumes, finding the line on the new path.

### `evac` (Custom Block & `when I receive evac`)
This block executes the complete victim rescue sequence.
1.  Stops line following.
2.  Moves forward and turns to align with the victim.
3.  Lowers the forklift arm (Motor F).
4.  Drives forward to secure the victim.
5.  Raises the forklift arm.
6.  Turns back towards the designated drop-off box.
7.  Drives forward to the box.
8.  Lowers the arm to deposit the victim.
9.  Backs away to complete the run.

## 4. How to Use

1.  Construct the robot according to the hardware specification above.
2.  Connect all motors and sensors to the correct ports.
3.  Download this program file to the LEGO Spike Prime Hub.
4.  Place the robot on the course, with the two line sensors (C & D) straddling the black line.
5.  Run the program from the Hub.
