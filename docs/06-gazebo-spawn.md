

Gazebo installation completed

Now that Gazebo Fortress is working on your Ubuntu laptop, we will spawn your existing URDF robot inside the simulation.

## **1. What does spawning mean?**

**Spawning** means inserting a robot model into a running simulation.

We already have a `robot_description` package containing your URDF model, which we visualized in RViz2.

We will reuse that model rather than create a new robot.

## **2. Understand the URDF requirements**

In RViz2, we mainly needed:

- `<link>` — defines robot parts.
    
- `<joint>` — connects links.
    
- `<visual>` — describes appearance.
    

Gazebo also needs physical properties:

|URDF element|Purpose|
|---|---|
|`<visual>`|Appearance of the robot|
|`<collision>`|Shape used for collision detection|
|`<inertial>`|Mass and rotational inertia|
|`<joint>`|Connections and motion constraints|

For example, a robot chassis can have:

```xml
<link name="base_link">

  <visual>
    <geometry>
      <box size="0.4 0.3 0.1"/>
    </geometry>
  </visual>

  <collision>
    <geometry>
      <box size="0.4 0.3 0.1"/>
    </geometry>
  </collision>

  <inertial>
    <mass value="5.0"/>
    <inertia
      ixx="0.0417"
      ixy="0.0"
      ixz="0.0"
      iyy="0.0708"
      iyz="0.0"
      izz="0.1042"/>
  </inertial>

</link>
```

Here, the robot chassis is a box measuring 40 × 30 × 10 cm with a mass of 5 kg. The inertia values are approximate values for a uniform box of those dimensions.

## **3. Find your existing URDF**

Open a terminal on your laptop:

```bash
cd ~/ros2_ws/src/robot_description
```

Check the package contents:

```bash
ls
```

Find your URDF files:

```bash
find . -type f \( -name "*.urdf" -o -name "*.xacro" \)
```

You might see something like:

```text
./urdf/robot.urdf
```

or:

```text
./urdf/robot.urdf.xacro
```

We need the actual file name before proceeding.

## **4. Spawn the robot**

First, make sure Gazebo is running in another terminal:

```bash
ign gazebo empty.sdf
```

In a second terminal, source ROS 2:

```bash
source /opt/ros/humble/setup.bash
source ~/ros2_ws/install/setup.bash
```

If your model is a plain URDF file, use:

```bash
ros2 run ros_gz_sim create \
  -world empty \
  -file ~/ros2_ws/src/robot_description/urdf/robot.urdf \
  -name my_robot \
  -x 0 -y 0 -z 0.5
```

Replace `robot.urdf` with your actual filename.

### **Explanation**

|Parameter|Meaning|
|---|---|
|`create`|Spawn a model into Gazebo|
|`-world empty`|Target the world named `empty`|
|`-file`|Path to robot description|
|`-name my_robot`|Name of the spawned robot|
|`-x 0 -y 0`|Initial horizontal position|
|`-z 0.5`|Initial height of 0.5 m|

The robot starts above the ground. With valid collision and inertial properties, it should fall under gravity and settle on the floor.

If your model is Xacro, it must first be converted to URDF before spawning.

**5. Verify the result**

Once the robot appears, you can inspect the simulation and check whether it interacts with gravity and the floor correctly.

---

[Next: Make your robot move in Gazebo](./07-gazebo-movement.md)
