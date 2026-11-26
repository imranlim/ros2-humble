# Add a LiDAR Sensor to a URDF

## **5. Adding a LiDAR sensor to the robot**

Our robot description is stored at:

```text
/ros2_ws/src/robot_description/urdf/my_robot_generated.urdf
```

We added three main components:

- `lidar_link` — the sensor's physical and coordinate-frame representation.
    
- `lidar_joint` — attaches the sensor to the robot.
    
- Gazebo sensor definition — generates simulated LiDAR data.
    

### **5.1 LiDAR link**

```xml
<link name="lidar_link">
  <visual>
    <geometry>
      <cylinder radius="0.05" length="0.04"/>
    </geometry>
    <material name="lidar_black">
      <color rgba="0.1 0.1 0.1 1"/>
    </material>
  </visual>

  <inertial>
    <mass value="0.1"/>
    <inertia
      ixx="0.00005" ixy="0" ixz="0"
      iyy="0.00005" iyz="0"
      izz="0.000125"/>
  </inertial>
</link>
```

The visual geometry represents a small cylindrical sensor mounted on the robot.

The inertial properties describe its approximate mass and rotational inertia for simulation.

### **5.2 Fixed joint**

```xml
<joint name="lidar_joint" type="fixed">
  <parent link="base_link"/>
  <child link="lidar_link"/>
  <origin xyz="0 0 0.2" rpy="0 0 0"/>
</joint>
```

**Explanation:**

|Element|Meaning|
|---|---|
|`parent`|Robot's base link|
|`child`|LiDAR link|
|`fixed`|No relative movement|
|`xyz`|Position relative to the parent|
|`rpy`|Roll, pitch, yaw orientation|

Here, the LiDAR is positioned 0.2 metres above the `base_link` origin.

This joint also establishes the fixed transform:

```text
base_link
    |
    └── lidar_link
```

### **5.3 Gazebo sensor definition**

```xml
<gazebo reference="lidar_link">
  <sensor name="lidar_sensor" type="gpu_lidar">
    <always_on>true</always_on>
    <update_rate>10</update_rate>
    <topic>scan</topic>
    <ignition_frame_id>lidar_link</ignition_frame_id>

    <lidar>
      <scan>
        <horizontal>
          <samples>360</samples>
          <resolution>1</resolution>
          <min_angle>-3.14159</min_angle>
          <max_angle>3.14159</max_angle>
        </horizontal>
      </scan>

      <range>
        <min>0.12</min>
        <max>8.0</max>
        <resolution>0.01</resolution>
      </range>
    </lidar>
  </sensor>
</gazebo>
```

### **Sensor parameters**

|Parameter|Configured value|Purpose|
|---|---|---|
|Sensor type|`gpu_lidar`|GPU-based ray scanning|
|Update rate|10 Hz|Ten scans per simulation second|
|Samples|360|Angular samples|
|Minimum angle|-180°|Beginning of scan|
|Maximum angle|+180°|End of scan|
|Minimum range|0.12 m|Closest measurable distance|
|Maximum range|8.0 m|Maximum measurable distance|
|Topic|`/scan`|Sensor output topic|
|Frame|`lidar_link`|Sensor coordinate frame|

### **Why GPU LiDAR?**

Gazebo's GPU LiDAR uses rendering-based ray simulation to estimate distances to surrounding geometry.

It is useful for simulated perception systems, especially when many rays must be processed.

The exact performance depends on GPU drivers, rendering configuration, and simulation complexity.

---

[Next: Configure a Gazebo world for LiDAR](./lidar-gazebo-world.md)
