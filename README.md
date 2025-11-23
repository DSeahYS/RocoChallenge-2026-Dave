# 🤖 RoCo Challenge 2026 - Gearbox Assembly

[![Isaac Sim](https://img.shields.io/badge/Isaac%20Sim-5.1-76B900?logo=nvidia)](https://docs.isaacsim.omniverse.nvidia.com/)
[![Isaac Lab](https://img.shields.io/badge/Isaac%20Lab-2.3-silver)](https://isaac-sim.github.io/IsaacLab/)
[![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-BSD--3-yellow)](LICENSE)

> **Personal repository for the [RoCo Challenge@AAAI 2026](https://rocochallenge.github.io/RoCo2026/doc.html)** - Bimanual robotic assembly of planetary gearboxes using the Galaxea R1 robot in Isaac Sim.

![RoCo Challenge](docs/images/poster.png)

---

## 🎯 Challenge Overview

The Gearbox Assembly Assistance Challenge evaluates **bimanual robotic systems** in collaborative manufacturing environments. The robot must autonomously assemble a planetary gearbox with multiple precision components.

### What This Repository Does

- **Simulates** the Galaxea R1 dual-arm robot in NVIDIA Isaac Sim
- **Assembles** a planetary gearbox with 7 components (ring gear, 4 sun gears, carrier, reducer)
- **Provides** rule-based and RL-trainable agents for autonomous assembly
- **Evaluates** assembly accuracy and coordination

---

## 🖥️ System Requirements

### Hardware
- **GPU**: NVIDIA RTX series (RTX 5080 22GB used in this setup)
- **RAM**: 16GB+ recommended
- **Storage**: ~30GB for Isaac Sim + Isaac Lab + this project
- **OS**: Windows 10/11 or Linux

### Software Dependencies
| Component | Version | Purpose |
|-----------|---------|---------|
| **NVIDIA Isaac Sim** | 5.0+ | 3D robotics simulation platform |
| **Isaac Lab** | 2.3+ | Robotics learning framework |
| **Python** | 3.11 | Runtime environment |
| **Git LFS** | 3.7+ | Large file storage for 3D models |
| **CUDA** | 12.x | GPU acceleration |

---

## 🚀 Installation

### Step 1: Install Isaac Sim & Isaac Lab

Follow the official [Isaac Lab installation guide](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/index.html).

**Recommended**: Use conda for easier Python environment management.

### Step 2: Clone This Repository

> **Important**: This repository uses Git LFS for 3D model files.

```bash
# Install Git LFS if not already installed
git lfs install

# Clone the repository
git clone https://github.com/DSeahYS/RocoChallenge-2026-Dave.git
cd RocoChallenge-2026-Dave

# Pull LFS files
git lfs pull
```

### Step 3: Install the Custom Extension

Using the Python environment that has Isaac Lab installed:

```bash
# If using Isaac Lab with conda
python -m pip install -e source/Galaxea_Lab_External

# If using Isaac Sim's Python directly
PATH_TO_ISAACLAB/isaaclab.bat -p -m pip install -e source/Galaxea_Lab_External
```

### Step 4: Verify Installation

List available environments:

```bash
# With conda
python scripts/list_envs.py

# With Isaac Lab script
PATH_TO_ISAACLAB/isaaclab.bat -p scripts/list_envs.py
```

You should see: `Template-Galaxea-Lab-External-Direct-v0`

---

## 🎮 Quick Start

### Test with Zero-Action Agent

```bash
python scripts/zero_agent.py --task=Template-Galaxea-Lab-External-Direct-v0
```

### Test with Random Actions

```bash
python scripts/random_agent.py --task=Template-Galaxea-Lab-External-Direct-v0
```

### **Run the Rule-Based Assembly Agent** (Recommended)

```bash
python scripts/rule_based_agent.py --task=Template-Galaxea-Lab-External-Direct-v0 --enable_cameras
```

This demonstrates the full assembly sequence with coordinated bimanual manipulation!

---

## 📁 Project Structure

```
RocoChallenge 2026/
├── source/
│   └── Galaxea_Lab_External/          # Main extension package
│       ├── Galaxea_Lab_External/
│       │   ├── robots/               # Robot configs & policies
│       │   │   ├── galaxea_rule_policy.py    (821 lines - assembly logic)
│       │   │   ├── galaxea_robots.py         (robot configuration)
│       │   │   └── gears_assets.py           (gearbox components)
│       │   └── tasks/
│       │       └── direct/
│       │           └── galaxea_lab_external/
│       │               ├── galaxea_lab_external_env.py     (505 lines)
│       │               └── galaxea_lab_external_env_cfg.py (config)
│       └── assets/                   # 3D models (managed with Git LFS)
│           ├── Gearbox/              # .usd gear models
│           └── Robots/               # Robot models
├── scripts/                          # Executable scripts
│   ├── list_envs.py                 # Environment listing
│   ├── zero_agent.py                # Zero-action test
│   ├── random_agent.py              # Random-action test
│   ├── rule_based_agent.py          # Main assembly demo
│   └── rl_games/                    # RL training scripts
└── docs/                            # Documentation & images
```

---

## 🤖 The Assembly Process

The rule-based agent performs a 6-step coordinated assembly:

1. **Initialize** - Position robot and randomize component placement on table
2. **Scan** - Use head and hand cameras to locate all gearbox parts
3. **Pick** - Dual arms pick up sun planetary gears from table
4. **Plan** - Determine optimal mounting strategy (which arm → which pin)
5. **Mount** - Precisely insert gears into planetary carrier pins
6. **Assemble** - Complete the gearbox assembly with ring gear and reducer

### Key Features

- ✅ **Multi-camera perception** (head camera + left/right hand cameras)
- ✅ **Differential IK control** for precise manipulation
- ✅ **Collision avoidance** between robot arms
- ✅ **Spatial reasoning** to avoid component overlap on table
- ✅ **Assembly scoring** system for evaluation

---

## 🧠 AI Agent Architecture

### Rule-Based Agent (`galaxea_rule_policy.py`)

**Finite State Machine** with 6 time-stepped phases:
- Phase 1: Approach and grasp preparation
- Phase 2: Gear pickup with gripper control
- Phase 3-6: Mounting sequence with coordinated bimanual movement

**Key Methods**:
- `prepare_mounting_plan()` - Assigns gears to arms and pins
- `pick_up_sun_planetary_gear()` - Grasping logic
- `mount_gear_to_planetary_carrier()` - Assembly insertion

### Environment (`galaxea_lab_external_env.py`)

**Reinforcement Learning Interface**:
- **Observation Space**: Robot state + object poses (16-dim)
- **Action Space**: Joint commands for dual arms + grippers (16-dim)
- **Reward Function**: Distance to target + assembly completion bonus
- **Episode Length**: 60 seconds

---

## 🛠️ Development Workflow

### Typical Workflow

1. **Edit code** in VSCode (`galaxea_rule_policy.py`, environment configs, etc.)
2. **Run simulation** from terminal using Isaac Lab Python
3. **View results** in Isaac Sim 3D viewport (runs in separate window)
4. **Iterate** based on assembly success/failure

### VSCode Setup (Optional)

Configure Python IntelliSense for Isaac Lab:

```bash
# From IsaacLab directory
PATH_TO_ISAACLAB/isaaclab.bat -v
```

This generates `.vscode/settings.json` with proper Python paths.

---

## 📊 Performance

**Tested Configuration**:
- GPU: NVIDIA RTX 5080 (22GB VRAM)
- CPU: Modern multi-core processor
- Performance: **115 FPS** @ 1280×720 rendering

**Expected Performance**:
- RTX 4090/5090: >100 FPS
- RTX 4070/5070: 60-80 FPS
- RTX 3080: 40-60 FPS

---

## 🐛 Troubleshooting

### Issue: "Isaac Sim directory not found"

**Solution**: Ensure Isaac Lab can locate Isaac Sim:
- Check for `_isaac_sim` symlink in Isaac Lab directory, OR
- Install Isaac Sim via pip: `conda install isaacsim-rl`

### Issue: "Environment not found when running list_envs.py"

**Solution**: Verify the extension is installed:
```bash
python -m pip show Galaxea_Lab_External
```

If not installed, run:
```bash
python -m pip install -e source/Galaxea_Lab_External
```

### Issue: Git LFS files not downloaded

**Solution**:
```bash
git lfs install
git lfs pull
```

### Issue: Pylance indexing errors in VSCode

**Solution**: Update VSCode settings to include extension path:
```json
{
    "python.analysis.extraPaths": [
        "C:/VSCode Folder/RocoChallenge 2026/source/Galaxea_Lab_External"
    ]
}
```

---

## 🎓 Learning Resources

- [Isaac Lab Documentation](https://isaac-sim.github.io/IsaacLab/)
- [Isaac Sim Tutorials](https://docs.isaacsim.omniverse.nvidia.com/latest/index.html)
- [RoCo Challenge Official Site](https://rocochallenge.github.io/RoCo2026/doc.html)
- [Differential IK Control](https://isaac-sim.github.io/IsaacLab/main/source/api/isaaclab/controllers/differential_ik.html)

---

## 📝 License

- **Source Code**: BSD-3-Clause License
- **Assets**: See individual asset licenses in `docs/licenses/`
- **Isaac Sim**: Proprietary license (see [Isaac Sim License](docs/licenses/dependencies/isaacsim-license.txt))

---

## 🙏 Acknowledgments

This project extends the **RoCo Challenge 2026** baseline provided by the challenge organizers. It builds on:
- **NVIDIA Isaac Sim & Isaac Lab** - Simulation platform
- **Galaxea R1 Robot** - Hardware platform
- **Challenge Organizers** - for the competition and baseline code

---

## 📧 Contact

**Repository Owner**: Dave  
**GitHub**: [@DSeahYS](https://github.com/DSeahYS)  
**Challenge**: [RoCo@AAAI 2026](https://rocochallenge.github.io/RoCo2026/)

---

**Built with ❤️ using NVIDIA Isaac Lab**
