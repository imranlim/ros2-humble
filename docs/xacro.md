
#  Xacro – Simplifying URDF

## 1. What is Xacro?

Xacro stands for XML Macros.

It is an extension of URDF that allows us to write robot descriptions using:

- Variables
    
- Mathematical expressions
    
- Reusable macros
    
- Properties
    

Imagine creating a robot with four wheels. In normal URDF, you would repeat similar XML code for every wheel.

With Xacro, you can define the wheel structure once and reuse it.

[Introducing OpenBot Waffle: An Open-Source Robotics Platform | ICI Innolabs](https://images.openai.com/static-rsc-4/xleertcFkiWykFy8qyB25jIJ1Q8Air0TgD9Nv9BPfrRQgxqxFTM-X76JhVl10yKiiVCh1hVJ9vtsmQwcyazcb0vcxnqxQ71JJo2QBEx5RXFWOSg-QXf_VKaJ7grU5M4fPClk1-8c_okNWmh-IHuvb5bt84m4UX_P1NfZNnR5k9Q?purpose=inline)

[innolabs.ro](https://www.innolabs.ro/blog/open-bot-waffle-a-robot-platform)

## 2. URDF vs Xacro

|URDF|Xacro|
|---|---|
|Plain XML|XML with macros and expressions|
|Repeated code|Reusable code|
|Values often hardcoded|Supports variables|
|Larger files|Easier to maintain|

Important: Xacro is not a replacement for URDF. It generates URDF from a more convenient XML description.

## 3. Install Xacro

On your Ubuntu laptop:

```bash
sudo apt update
sudo apt install ros-humble-xacro
```

## 4. Create a Xacro file

Go to your robot description package:

```bash
cd ~/ros2_ws/src/robot_description/urdf
```

Create:

```bash
nano my_robot.urdf.xacro
```

Add the following code.

```xml

<?xml version="1.0"?>

<robot name="my_robot" xmlns:xacro="http://www.ros.org/wiki/xacro">

    <!-- Define reusable values -->

    <xacro:property name="body_length" value="0.4"/>
    <xacro:property name="body_width" value="0.3"/>
    <xacro:property name="body_height" value="0.1"/>

    <xacro:property name="wheel_radius" value="0.08"/>
    <xacro:property name="wheel_width" value="0.04"/>


    <!-- Main robot body -->

    <link name="base_link">

        <visual>

            <geometry>
                <box size="${body_length} ${body_width} ${body_height}"/>
            </geometry>

            <material name="blue">
                <color rgba="0.1 0.3 0.8 1"/>
            </material>

        </visual>

    </link>


    <!-- Wheel macro -->

    <xacro:macro name="wheel" params="name y_position">

        <link name="${name}">

            <visual>

                <geometry>
                    <cylinder radius="${wheel_radius}" length="${wheel_width}"/>
                </geometry>

                <origin xyz="0 0 0" rpy="1.5708 0 0"/>

                <material name="black">
                    <color rgba="0.1 0.1 0.1 1"/>
                </material>

            </visual>

        </link>

        <joint name="${name}_joint" type="continuous">

            <parent link="base_link"/>
            <child link="${name}"/>

            <origin xyz="0 ${y_position} -0.08" rpy="0 0 0"/>

            <axis xyz="0 1 0"/>

        </joint>

    </xacro:macro>


    <!-- Create two wheels using the same macro -->

    <xacro:wheel name="left_wheel_link" y_position="0.17"/>

    <xacro:wheel name="right_wheel_link" y_position="-0.17"/>

</robot>
```

Save the file.

## 5. Understand the Xacro concepts

A. Properties

```xml
<xacro:property name="wheel_radius" value="0.08"/>
```

This creates a variable.

We can reuse it:

```xml
<cylinder radius="${wheel_radius}" length="${wheel_width}"/>
```

If we change the wheel radius once, every wheel using that property gets the new value.

B. Macros

```xml
<xacro:macro name="wheel" params="name y_position">
```

This defines a reusable block of XML.

It accepts two parameters:

- `name`: Wheel link name.
    
- `y_position`: Wheel position along the Y-axis.
    

C. Calling a macro

```xml
<xacro:wheel name="left_wheel_link" y_position="0.17"/>
```

This creates the left wheel.

```xml
<xacro:wheel name="right_wheel_link" y_position="-0.17"/>
```

This creates the right wheel.

Notice that we defined the wheel structure only once!

## 6. Convert Xacro into URDF

Before using it in ROS 2, let's convert the Xacro file into a normal URDF file.

Run:

```bash
cd ~/ros2_ws/src/robot_description/urdf

ros2 run xacro xacro my_robot.urdf.xacro -o my_robot_generated.urdf
```

This generates:

```text
my_robot_generated.urdf
```

Open it:

```bash
nano my_robot_generated.urdf
```

You will see ordinary URDF XML.

The Xacro properties and macros have been expanded into actual links and joints.

## 7. Check the generated robot

Run:

```text
check_urdf my_robot_generated.urdf
```

If the command is missing:

```bash
sudo apt install liburdfdom-tools
```

Expected output:

```text
robot name is: my_robot
---------- Successfully Parsed XML ---------------
root Link: base_link has 2 child(ren)
```

---
#  Load Xacro directly into RViz2

Previously, our launch file loaded:

```text
my_robot.urdf
```

Now we'll make it load:

```text
my_robot.urdf.xacro
```

ROS 2 will process the Xacro file automatically when launching.

## 1. Modify the launch file

Open your existing launch file:

```bash
cd ~/ros2_ws/src/robot_description/launch

nano display_robot.launch.py
```

Replace its contents with:

```python

import os
import xacro

from launch import LaunchDescription
from launch_ros.actions import Node
from ament_index_python.packages import get_package_share_directory


def generate_launch_description():

    package_path = get_package_share_directory(
        'robot_description'
    )

    xacro_file = os.path.join(
        package_path,
        'urdf',
        'my_robot.urdf.xacro'
    )

    # Process Xacro and convert it into URDF
    robot_description = xacro.process_file(xacro_file).toxml()

    return LaunchDescription([

        Node(
            package='robot_state_publisher',
            executable='robot_state_publisher',
            parameters=[{
                'robot_description': robot_description
            }]
        ),

        Node(
            package='joint_state_publisher_gui',
            executable='joint_state_publisher_gui'
        )

    ])

```

## 2. Understand the important change

Previously, we used:

```python
with open(urdf_file, 'r') as file:    robot_description = file.read()
```

This simply reads an existing URDF file.

Now we use:

```python
robot_description = xacro.process_file(xacro_file).toxml()
```

This does two things:

1. Processes the Xacro file, expanding properties and macros.
    
2. Converts the result into URDF XML.
    

The resulting URDF is passed to `robot_state_publisher`.

## 3. Add Xacro dependency

Open:

```bash
nano ~/ros2_ws/src/robot_description/package.xml
```

Make sure this dependency exists:

```xml
<exec_depend>xacro</exec_depend>
```

You should also have:

```xml
<exec_depend>robot_state_publisher</exec_depend>
<exec_depend>joint_state_publisher_gui</exec_depend>
<exec_depend>launch</exec_depend>
<exec_depend>launch_ros</exec_depend>
```

## 4. Build the package

```bash
cd ~/ros2_ws

colcon build --packages-select robot_description

source install/setup.bash
```

## 5. Launch your Xacro robot

```bash
ros2 launch robot_description display_robot.launch.py
```

Open another terminal:

```text
rviz2
```

In RViz2:

1. Set Fixed Frame to `base_link`.
    
2. Add the `RobotModel` display.
    
3. Check that the robot appears.
    
4. Move the wheel sliders.
    

Your robot should look and behave just like before, but now its description is generated directly from Xacro.

---

[Next: Add mass, inertia, and collision to URDF](./mass-inertia-collision.md)
