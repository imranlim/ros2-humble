# Gazebo Simulation

URDF + RViz2 completed

Since your robot model is now visible in RViz2 and its joints move using the sliders, the next topic is **Gazebo Simulation**.

Until now, we have visualized a robot. Next, we will make it interact with a simulated physical world.

## **1. What is Gazebo?**

Gazebo is a robotics simulator that provides:

- **Physics:** Gravity, friction, collisions and motion.
    
- **Sensors:** LiDAR, cameras, IMU and other simulated sensors.
    
- **Robot control:** Simulated motors and wheel movement.
    
- **Environment:** Floors, walls and obstacles.
    
- **ROS 2 integration:** Communication between Gazebo and ROS 2 nodes.
    

### **RViz2 vs Gazebo**

|RViz2|Gazebo|
|---|---|
|Visualization tool|Physics simulator|
|Displays robot model|Simulates robot movement|
|Displays TF and sensor data|Generates simulated sensor data|
|No physics simulation|Gravity and collision physics|
|Useful for debugging|Useful for testing robot behavior|

## **2. Our learning roadmap**

1. **Install Gazebo Fortress** — the Gazebo release paired with ROS 2 Humble
    
2. **Understand Gazebo architecture** — worlds, models, plugins and simulation
    
3. **Spawn our URDF robot** into Gazebo
    
4. **Add collision and inertial properties** to our robot
    
5. **Implement differential-drive control** for two wheels
    
6. **Integrate `ros2_control`** and controllers
    
7. **Drive using teleop** and keyboard commands
    
8. **Connect Gazebo with RViz2** to visualize TF and sensor data
    

## **3. Practical 1 — Install Gazebo Fortress**

We will run Gazebo on your **Ubuntu 22.04 laptop**, not your headless Raspberry Pi 4, because Gazebo's graphical interface is more suitable for the laptop.

Open a terminal on your laptop.

**Step 1 — Check ROS 2**

```bash
echo $ROS_DISTRO
```

Expected output:

```text
humble
```

**Step 2 — Install Gazebo integration**

```bash
sudo apt update

sudo apt install ros-humble-ros-gz
```

This installs the ROS 2 integration packages for Gazebo (`ros_gz_sim`, `ros_gz_bridge` and others) and pulls in Gazebo Fortress as a dependency.

> **Humble note:** ROS 2 Humble pairs with **Gazebo Fortress** (formerly called *Ignition Fortress*). Its binaries come straight from the ROS apt repository, so no extra Gazebo repository is needed. In Fortress the command-line tool is called `ign` instead of `gz`, so `gz sim` becomes `ign gazebo` and `gz topic` becomes `ign topic`. Also, message types are written `ignition.msgs.*` instead of `gz.msgs.*`.
>
> The old *Gazebo Classic* (`gazebo` / `gazebo_ros_pkgs`) is a different simulator and is not used in this roadmap.

**Step 3 — Check Gazebo**

```bash
ign gazebo --version
```

You should see version information for Gazebo Sim, for example `6.x.x` (Fortress uses the Ignition Gazebo 6 series).

**Step 4 — Launch an empty world**

```bash
ign gazebo empty.sdf
```

Official reference: [Use ROS 2 to interact with Gazebo (Fortress)](https://gazebosim.org/docs/fortress/ros2_integration/)

A Gazebo window should open with an empty simulation environment.

## **4. Understand how ROS 2 communicates with Gazebo**

Bidirectional communication between ROS 2 and Gazebo through ros_gz_bridge

The `ros_gz_bridge` package allows messages to pass between ROS 2 topics and Gazebo Transport topics.

For example, ROS 2 can send velocity commands to a simulated robot, while Gazebo can provide odometry and sensor readings back to ROS 2.

## **Your task**

Run these commands on your laptop:

```bash
ign gazebo --version
ign gazebo empty.sdf
```

---

[Next: Spawn your robot in Gazebo](./06-gazebo-spawn.md)
