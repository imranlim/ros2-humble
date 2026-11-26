#  Introduction to TF2

## 1. What is TF2?

TF2 stands for Transform Library 2. It manages the relationships between coordinate frames in a robot.

For example, consider a differential-drive robot with:

- A main body (`base_link`)
    
- A LiDAR sensor (`laser_link`)
    
- A left wheel (`left_wheel_link`)
    
- A right wheel (`right_wheel_link`)
    

Each part has its own coordinate frame.

[ROS 2 Navigation: Transformations](https://images.openai.com/static-rsc-4/JUrzSCwdtj9COJe_rdImjSHe5y9S2MFI90xGNqqZNZFnYlS329ZEvK5G0r35IwNrumH5iA_vvFuZ7pnw0Ig1V5-XLqBYws3wjuakys_m11daM85pOIf8oxta8ikEiJSXD2C3qltxtwOQ7VOvs5E8tGEH78MFBJKwSCdjlMH4ZM4?purpose=inline)

[robotics.snowcron.com](https://robotics.snowcron.com/robotics_ros2/nav_basics_transformations.htm)

TF2 helps answer questions such as:

- Where is the LiDAR sensor relative to the robot body?
    
- Where is the left wheel relative to the robot?
    
- Where is the robot relative to the world?
    

## 2. Understanding coordinate frames

A coordinate frame describes a position and orientation using three axes:

- X: Forward direction
    
- Y: Left direction
    
- Z: Up direction
    

For a typical mobile robot:

|Frame|Purpose|
|---|---|
|`world`|Fixed reference for the environment|
|`base_link`|Main robot body|
|`laser_link`|LiDAR sensor position|
|`left_wheel_link`|Left wheel|
|`right_wheel_link`|Right wheel|

A simplified TF tree looks like this:

world

base_link

laser_link

left_wheel_link

right_wheel_link

This is a simplified example. A real robot's TF tree may have additional frames.

## 3. Static and dynamic transformations

There are two important types of transformations.

Static transformation

A fixed relationship between two frames.

Example: A LiDAR sensor is permanently mounted 20 cm above the robot body.

```text
base_link → laser_link
```

Dynamic transformation

A relationship that changes over time.

Example: The robot moves through a room.

```text
world → base_link
```

## 4. Practical: Create a static TF broadcaster

We will create a Python node that publishes the fixed position of a LiDAR sensor relative to the robot body.

Use your Ubuntu laptop.

### Step 1: Create the Python file

```bash
cd ~/ros2_ws/src/robot_basics/robot_basics
touch static_tf_broadcaster.py
```

Open it:

```bash
nano static_tf_broadcaster.py
```

### Step 2: Add this code

```python
import rclpy

from rclpy.node import Node
from geometry_msgs.msg import TransformStamped
from tf2_ros.static_transform_broadcaster import StaticTransformBroadcaster


class StaticTFBroadcaster(Node):

    def __init__(self):
        super().__init__('static_tf_broadcaster')

        self.broadcaster = StaticTransformBroadcaster(self)

        transform = TransformStamped()

        transform.header.stamp = self.get_clock().now().to_msg()

        transform.header.frame_id = 'base_link'
        transform.child_frame_id = 'laser_link'

        # LiDAR position relative to robot body
        transform.transform.translation.x = 0.2
        transform.transform.translation.y = 0.0
        transform.transform.translation.z = 0.2

        # No rotation
        transform.transform.rotation.x = 0.0
        transform.transform.rotation.y = 0.0
        transform.transform.rotation.z = 0.0
        transform.transform.rotation.w = 1.0

        self.broadcaster.sendTransform(transform)

        self.get_logger().info('Static TF published!')


def main(args=None):

    rclpy.init(args=args)

    node = StaticTFBroadcaster()

    rclpy.spin(node)

    node.destroy_node()

    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

### Step 3: Understand the important values

```python
transform.header.frame_id = 'base_link'
transform.child_frame_id = 'laser_link'
```

This means the LiDAR frame is described relative to the robot body frame.

```python
transform.transform.translation.x = 0.2
transform.transform.translation.y = 0.0
transform.transform.translation.z = 0.2
```

The LiDAR is positioned:

- 0.2 metres forward
    
- 0 metres sideways
    
- 0.2 metres upward
    

The quaternion values represent no rotation.

### Step 4: Add the executable

Open:

```bash
nano ~/ros2_ws/src/robot_basics/setup.py
```

Add this inside the existing `console_scripts` list:

```python
'static_tf_broadcaster = robot_basics.static_tf_broadcaster:main',
```

Keep your other entries.

### Step 5: Build the package

```bash
cd ~/ros2_ws
colcon build --packages-select robot_basics
source install/setup.bash
```

### Step 6: Run the broadcaster

```bash
ros2 run robot_basics static_tf_broadcaster
```

Expected output:

```text
Static TF published!
```

Keep this terminal running.

## 5. Check the transformation

Open a second terminal on your laptop.

```bash
source /opt/ros/humble/setup.bash
source ~/ros2_ws/install/setup.bash
```

Check the TF topics:

```bash
ros2 topic list | grep tf
```

You should see:

```text
/tf
/tf_static
```

Check the transformation:

```bash
ros2 run tf2_ros tf2_echo base_link laser_link
```

You should see translation values approximately like:
```text
Translation:
  x: 0.2
  y: 0.0
  z: 0.2
```

---

[Next: Dynamic TF broadcaster](./dynamic-tf.md)
