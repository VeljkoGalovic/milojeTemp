# miloje — LiDAR Test Workspace

ROS 2 workspace for experimenting with an **RPLidar A1**: a custom sweep
message, an on-demand scan node, and tooling for inspecting recorded bags.

## Contents

| Path              | What                                                                                   |
|-------------------|----------------------------------------------------------------------------------------|
| `src/lidar_msgs/` | Custom ROS 2 message package: `LidarSweep` (`angles[]`, `distances[]` + header)         |
| `src/lidar_pkg/`  | ROS 2 nodes for the RPLidar A1 (see below)                                              |
| `runner.py`       | Standalone helper to deserialize the first `LidarSweep` from a recorded `.db3` bag       |
| `lidar_test_data/`| Recorded test bags (SQLite `db3` format) used by `runner.py`                              |

## The nodes

- `lidar_scan_node.py` — connects to the lidar at startup, then stays idle until a
  peer calls the `~/scan_request` service (`std_srvs/Trigger`). On trigger it
  collects exactly one full 360° rotation, converts it to a
  `sensor_msgs/LaserScan`, publishes it on `~/scan`, and returns to idle.
  Blocking lidar I/O runs in a background thread so the ROS executor is never
  blocked while waiting for hardware.
- `vector_deducer_node.py`, `path_planner_node.py`, `motion_planner_node.py`,
  `slam_mapping_node.py` — downstream exploration nodes that consume scans and
  drive simple planning/motion experiments (work-in-progress).

## Usage

```bash
# Build and source (ROS 2 workspace)
sudo apt install ros-${ROS_DISTRO}-rplidar-ros 2>/dev/null  # or vendor the RPLidar driver
colcon build && source install/setup.bash
ros2 run lidar_pkg lidar_scan_node.py
```

```bash
# Inspect a recorded sweep without a ROS runtime beyond Python packages
python3 runner.py
```

Sweeps used for testing: `lidar_test_data/lidar_test_data_0.db3`.

## Status

Archived — proof-of-concept from a robotics prototyping phase.
