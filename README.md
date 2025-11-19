# Matter Phase Simulation (Van der Waals)

A high-performance, interactive particle simulation written in **Rust** that models thermodynamic phase transitions (gas, liquid, solid).

This project simulates thousands of particles interacting via **Van der Waals forces** (modeled by the Lennard-Jones potential) in real-time. It utilizes a custom **spatial partitioning system** to optimize $O(N^2)$ N-body interactions down to near-linear time, enabling the simulation of complex emergent behaviors like crystalline formation and annealing.

## Key Features

  * **High-Performance Physics Engine:** Written in Rust using **Rayon** for data-parallelism, utilizing multi-core processing for physics calculations.
  * **Spatial Optimization:** Implements a uniform grid-based spatial partition algorithm to efficiently cull non-interacting particle pairs.
  * **Real-Time Visualization:** Built on the **Bevy** game engine (ECS architecture) for efficient rendering and state management.
  * **Interactive Thermodynamics:** Live control over simulation boundaries (volume), temperature injection, and pressure pinning to observe phase changes in real-time.
  * **Data Visualization:** Integrated plotting (via `egui`) of Pressure-Volume-Temperature (PVT) relationships and system Energy (Kinetic/Potential).

## Technical Implementation

### Physics Model

The simulation integrates Newton's laws of motion using a **Leapfrog integration** scheme for symplectic energy conservation. The inter-particle force is derived from the **Lennard-Jones potential**:

$$V_{LJ}(r) = 4\epsilon \left[ \left(\frac{\sigma}{r}\right)^{12} - \left(\frac{\sigma}{r}\right)^6 \right]$$

### Optimization: Spatial Grid

To avoid the $O(N^2)$ computational complexity of checking every particle against every other particle, the simulation space is divided into a uniform grid.

1.  **Grid Construction:** At the start of every frame, particles are hashed into grid cells based on their position.
2.  **Neighbor Search:** Forces are only calculated between particles in the same or adjacent cells.
3.  **Parallelization:** The force calculation loop is parallelized using `Rayon`, distributing particle chunks across available CPU threads.

### Architecture

The project follows **Data-Oriented Design** principles via Bevy's Entity Component System (ECS):

  * **Resources:** Global simulation state (Temperature, Pressure history, Boundary dimensions).
  * **Systems:** Logic pipelines (e.g., `advance_simulation`, `update_renders`) that run in parallel where possible.

## Setup & Usage

### Prerequisites

  * **Rust Toolchain:** Ensure you have the latest stable Rust installed (`rustup`).
  * **System Dependencies:** Linux users may need `libasound2-dev`, `libudev-dev`, and `libx11-xcb-dev` (Bevy dependencies).

### Installation

```bash
git clone https://github.com/nguyenston/van_der_waals_interactions.git
cd van_der_waals_interactions
# Build in release mode for optimization (crucial for particle count)
cargo run --release
```

### Controls

  * **W/A/S/D**: Move Camera
  * **Space/LShift**: Move Up/Down
  * **UI Panel**:
      * **Target Temperature**: Inject or remove energy to simulate heating/cooling.
      * **Boundary**: Expand or contract the simulation volume to change pressure.
      * **Pin Pressure**: Automatically adjust volume to maintain a target pressure (isobaric process).

## Project Structure

```text
src/
├── state/
│   ├── sim_space.rs      # Grid partitioning and Boundary logic
│   ├── physics.rs        # Lennard-Jones potential calculations
│   ├── sim_systems.rs    # Physics integration loops (Rayon parallel iterators)
│   └── particle.rs       # Particle data structure (POD)
├── main.rs               # App entry point and plugin assembly
└── ui_systems.rs         # Egui integration for real-time plotting
```
