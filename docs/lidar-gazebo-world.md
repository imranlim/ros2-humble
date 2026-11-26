# Configure a Gazebo World for LiDAR

## **6. The problem we encountered**

Initially, we launched:

```bash
ign gazebo -r empty.sdf
```

Our robot spawned successfully, but:

```bash
ign topic -l
```

did not display `/scan`.

We verified that the URDF contained:

```text
lidar_link
lidar_joint
lidar_sensor
gpu_lidar
```

This indicated that the sensor was defined, but the simulation was not producing its scan topic.

### **Root cause**

The working fix was to create a custom Gazebo world that explicitly loads the **Sensors system**.

The Sensors system is responsible for updating simulated sensors such as LiDAR and cameras.

## **7. Creating the custom world**

Our world file:

```text
/ros2_ws/worlds/lidar_world.sdf
```

Create or edit it using:

```bash
mkdir -p /ros2_ws/worlds
nano /ros2_ws/worlds/lidar_world.sdf
```

### **Essential Gazebo systems**

```xml
<plugin filename="ignition-gazebo-physics-system"
        name="ignition::gazebo::systems::Physics"/>

<plugin filename="ignition-gazebo-user-commands-system"
        name="ignition::gazebo::systems::UserCommands"/>

<plugin filename="ignition-gazebo-scene-broadcaster-system"
        name="ignition::gazebo::systems::SceneBroadcaster"/>

<plugin filename="ignition-gazebo-sensors-system"
        name="ignition::gazebo::systems::Sensors">
  <render_engine>ogre2</render_engine>
</plugin>
```

|System|Function|
|---|---|
|Physics|Gravity, collisions, dynamics|
|UserCommands|Spawn, remove, and modify entities|
|SceneBroadcaster|Publishes scene information for visualization|
|Sensors|Updates simulated LiDAR and other sensors|

The most important plugin for this lesson was:

```xml
<plugin
  filename="ignition-gazebo-sensors-system"
  name="ignition::gazebo::systems::Sensors">
  <render_engine>ogre2</render_engine>
</plugin>
```

Without a functioning Sensors system, a valid LiDAR sensor definition may not generate measurements.

## **8. Second problem — Missing Gazebo models**

Our first custom world attempted to load:

```xml
<include>
  <uri>model://ground_plane</uri>
</include>

<include>
  <uri>model://sun</uri>
</include>
```

Gazebo reported:

```text
Error Code 14:
Unable to find uri[model://ground_plane]

Unable to find uri[model://sun]
```

### **Why?**

Gazebo could not resolve those model resources in its configured resource paths.

### **Solution**

We replaced the external model references with definitions directly inside the world file.

For the ground:

```xml
<model name="ground_plane">
  <static>true</static>
  <link name="ground_link">
    <collision name="collision">
      <geometry>
        <plane>
          <normal>0 0 1</normal>
          <size>100 100</size>
        </plane>
      </geometry>
    </collision>
    <visual name="visual">
      <geometry>
        <plane>
          <normal>0 0 1</normal>
          <size>100 100</size>
        </plane>
      </geometry>
    </visual>
  </link>
</model>
```

We also defined a directional light directly in the SDF file.

This eliminated the dependency on external `model://` resources.

## **9. Adding an obstacle**

We added a static box to test LiDAR detection:

```xml
<model name="obstacle_box">
  <static>true</static>
  <pose>2 0 0.5 0 0 0</pose>

  <link name="box_link">
    <collision name="collision">
      <geometry>
        <box>
          <size>1 1 1</size>
        </box>
      </geometry>
    </collision>

    <visual name="visual">
      <geometry>
        <box>
          <size>1 1 1</size>
        </box>
      </geometry>
    </visual>
  </link>
</model>
```

The box is centred at X = 2 m, Y = 0 m and Z = 0.5 m in the world coordinate frame.

Its dimensions are 1 × 1 × 1 m.

As the robot approaches the box, the LiDAR should measure decreasing distances to its visible surface.

---

[Next: Run and debug the LiDAR simulation](./lidar-operation.md)
