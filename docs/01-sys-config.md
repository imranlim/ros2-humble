# System Configuration
### Prerequisites
**Ubuntu laptop**
- Ubuntu 22.04.5
- x86_64 architecture
- 16 GB RAM (recommended)
- ROS 2 Humble

**Raspberry Pi 4**
- Ubuntu 22.04.5 LTS Server
- ARM64 architecture
- 8 GB RAM

I have used this configuration for this roadmap. The future code and commands will be based on this config.

## 1. System Check

### 1.1 Ubuntu version
Run:

```bash
lsb_release -a
```

Expected output should include:

```bash
Description: Ubuntu 22.04.x LTS
Codename: jammy
```

### 1.2 Check processor architecture

Run:

```bash
uname -m
```

On the Ubuntu laptop, expected:

```bash
x86_64
```
This means the laptop is running 64-bit x86 Linux.

On the Raspberry Pi, the expected output is:

```text
aarch64
```

This means the Pi is running 64-bit ARM Linux.

### 1.3 Check Raspberry Pi hardware

Run:

```bash
cat /proc/device-tree/model
```

Expected:

```bash
Raspberry Pi 4 Model B
```

### 1.4 Check available memory

Run:

```bash
free -h
```
This tells us how much RAM is available.

### 1.5 Check whether ROS 2 is already installed

Run:

```bash
printenv ROS_DISTRO
```

Then:

```bash
ls /opt/ros/
```

If ROS is installed, you may see:

```bash
humble
```

If the directory doesn't exist or the environment variable is empty, ROS may not be installed or sourced.

If ROS 2 Humble is not installed yet, follow the official guide: [Install ROS 2 Humble on Ubuntu 22.04 (deb packages)](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html). On the laptop, install the desktop variant:

```bash
sudo apt install ros-humble-desktop
```

On the Raspberry Pi (headless), the base variant is enough:

```bash
sudo apt install ros-humble-ros-base
```

Then source it:

```bash
source /opt/ros/humble/setup.bash
```

---

## 2. Update Ubuntu

Once the system checks are complete, update Ubuntu.

```bash
sudo apt update
sudo apt upgrade -y
```

Install essential development tools:

```bash
sudo apt install -y \
  curl \
  wget \
  git \
  nano \
  build-essential \
  cmake \
  python3-pip \
  python3-venv \
  python3-colcon-common-extensions \
  python3-rosdep \
  python3-vcstool
```

What are these tools?
| Tool            | Purpose                               |
| --------------- | ------------------------------------- |
| Git             | Download and manage robotics projects |
| CMake           | Build C++ programs                    |
| colcon          | Build ROS 2 workspaces                |
| rosdep          | Install package dependencies          |
| vcstool         | Manage multiple repositories          |
| nano            | Edit configuration and code           |
| build-essential | C/C++ compiler and development tools  |
### Humble Check
```bash
echo $ROS_DISTRO
```

Expected:

```bash
humble
```

## 3. Create your first ROS 2 workspace

A workspace is a directory where you develop and build ROS 2 packages.

We'll keep all our project files organized.

Run:

```bash
mkdir -p ~/ros2_ws/src
```

Navigate:

```bash
cd ~/ros2_ws
```

Build the workspace:

```bash
colcon build
```

Source it:

```bash
source install/setup.bash
```

Your directory structure will look like:

```text
ros2_ws/
│
├── src/
│
├── build/
│
├── install/
│
└── log/
```

We'll eventually create our robot package inside `src`.

## 4. Test ROS 2 communication

Before building a robot, we should verify that ROS 2 nodes can communicate.

Open Terminal 1:

```bash
ros2 run demo_nodes_cpp talker
```

This node publishes messages.

Open Terminal 2:

```bash
ros2 run demo_nodes_cpp listener
```

What just happened?

- Talker is a ROS 2 publisher.
- Listener is a ROS 2 subscriber.
- Both communicate through a ROS 2 topic.
- The topic is `/chatter`.

This is the foundation of ROS 2 communication.

You can inspect it using another terminal:

```bash
ros2 topic list
```

Expected:

```text
/chatter
/parameter_events
/rosout
```

View the messages:

```bash
ros2 topic echo /chatter
```

## 5. Configure the same ROS domain

ROS 2 uses a domain ID to separate communication groups.
Both computers must use the same domain ID to discover each other.

We'll use:

```bash
export ROS_DOMAIN_ID=23
```

### On Raspberry Pi

Execute inside your SSH terminal:

```bash
export ROS_DOMAIN_ID=23
export ROS_LOCALHOST_ONLY=0
```

### On Ubuntu laptop

Open a normal terminal:

```bash
export ROS_DOMAIN_ID=23
export ROS_LOCALHOST_ONLY=0
```

Both computers now use domain ID 23

Terminal 1 – Ubuntu laptop:

```bash
ros2 run demo_nodes_cpp talker
```

Terminal 2 – Raspberry Pi SSH:

```bash
ros2 run demo_nodes_py listener
```

Your Raspberry Pi should receive messages from the laptop.

This confirms bidirectional communication.

### Make the configuration permanent

Only after both experiments work, add the settings to both computers.

On the Pi and laptop, execute:

```bash
echo "export ROS_DOMAIN_ID=23" >> ~/.bashrc

echo "export ROS_LOCALHOST_ONLY=0" >> ~/.bashrc
```

Apply:

```bash
source ~/.bashrc
```


### Your final network configuration

| Setting       | Raspberry Pi   | Ubuntu laptop |
| ------------- | -------------- | ------------- |
| IP address    | `192.168.1.12`(Your IP) | `192.168.1.9`(Your IP) |
| Ubuntu        | 22.04.5        | 22.04.5       |
| ROS 2         | Humble         | Humble        |
| ROS_DOMAIN_ID | 23             | 23            |
| Architecture  | ARM64          | x86_64        |
| Communication | Working        | Working       |

---

[Next: ROS 2 fundamentals](./02-ros2-fundamentals.md)
