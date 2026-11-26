# ROS 2 Humble — Robotics Learning Roadmap

A hands-on, beginner-friendly guide to learning ROS 2 Humble, robot simulation, control, perception, SLAM, and autonomous navigation.

## About This Repository

This repository documents my journey learning ROS 2 through practical robotics experiments using Ubuntu 22.04, ROS 2 Humble, Gazebo Fortress, RViz2, and Raspberry Pi 4.

Each tutorial explains:
- **What** the technology is
- **Why** it is needed
- **How** to implement it
- How to verify and troubleshoot it
- How it is used in real-world robotics

## Prerequisites

Before starting, make sure you have:

- Ubuntu 22.04 on a laptop or desktop
- Basic Linux terminal and command-line skills
- Basic Python knowledge
- Git installed
- A working internet connection for installing ROS 2 packages

No previous ROS 2 experience is required. The first lesson guides you through
the system checks and ROS 2 setup.


## Learning path

Follow the lessons in order. Each lesson links to the next one so you can continue
without returning to this page.

1. [System configuration](docs/01-sys-config.md)
2. [ROS 2 fundamentals](docs/02-ros2-fundamentals.md)
3. [Services](docs/services.md)
4. [Actions](docs/actions.md)
5. [Parameters](docs/parameters.md)
6. [Launch files](docs/launch-file.md)
7. [Introduction to TF2](docs/tf2.md)
8. [Dynamic TF broadcaster](docs/dynamic-tf.md)
9. [Visualizing TF2 in RViz2](docs/rviz2.md)
10. [URDF: Create your first robot model](docs/urdf.md)
11. [Xacro: Simplify URDF](docs/xacro.md)
12. [Add mass, inertia, and collision to URDF](docs/mass-inertia-collision.md)
13. [Gazebo simulation](docs/05-gazebo.md)
14. [Spawn your robot in Gazebo](docs/06-gazebo-spawn.md)
15. [Make your robot move in Gazebo](docs/07-gazebo-movement.md)
16. [Control Gazebo with ROS 2](docs/08-gazebo-ros2-control.md)
17. [Odometry, TF2, and RViz2 integration](docs/odometry-integration.md)
18. [TF2 and coordinate transformations](docs/coordinate-transformations.md)
19. [ros2_control](docs/ros2-control.md)
20. [LiDAR fundamentals](docs/lidar.md)
21. [Add a LiDAR sensor to a URDF](docs/lidar-urdf.md)
22. [Configure a Gazebo world for LiDAR](docs/lidar-gazebo-world.md)
23. [Run and debug the LiDAR simulation](docs/lidar-operation.md)

## Goal

To provide reproducible, practical tutorials for students, robotics enthusiasts, and developers getting started with ROS 2.

## Humble-specific notes

This version of the roadmap is adapted for **Ubuntu 22.04 (Jammy) + ROS 2 Humble**. The main differences from the Jazzy edition are:

| Topic | Jazzy edition | Humble edition (this repo) |
| --- | --- | --- |
| Ubuntu | 24.04 (noble) | 22.04 (jammy) |
| Gazebo | Harmonic (`gz sim`, `gz topic`) | Fortress (`ign gazebo`, `ign topic`) |
| Gazebo message types | `gz.msgs.*` | `ignition.msgs.*` |
| Gazebo plugin names | `gz-sim-*-system`, `gz::sim::systems::*` | `ignition-gazebo-*-system`, `ignition::gazebo::systems::*` |
| ros2_control + Gazebo | `gz_ros2_control` | `ign_ros2_control` |
| `diff_drive_controller` commands | `TwistStamped` on `cmd_vel` | `Twist` on `cmd_vel_unstamped` (`use_stamped_vel: false`) |
| `teleop_twist_keyboard` | `-p stamped:=true` | no `stamped` parameter needed |

The screenshots in `images/` come from the original lessons, so a few windows (for example the Gazebo GUI) may look slightly different on Fortress.

*This is an independent learning resource, not official ROS documentation.*
*The repository is available under the [MIT License](LICENSE).*
