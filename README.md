# Artemis II Free-Return Trajectory Simulation

A complete GMAT simulation of the Artemis II free-return lunar trajectory, covering the full mission profile from parking orbit through trans-lunar injection, lunar far-side flyby, and passive Earth return.

**Author**: Mason Nixon, Ph.D. -- [OrbitLink Consulting, LLC](https://orbitlink.org)

## Overview

Artemis II will carry astronauts around the Moon for the first time since Apollo 17 in 1972. This simulation models the complete trajectory using NASA's General Mission Analysis Tool (GMAT), with a two-phase differential correction targeting approach that converges on a free-return lunar flyby trajectory.

The trans-lunar injection is modeled as a single impulsive burn of approximately **3.15 km/s** from a 185 km parking orbit. The differential corrector adjusts the parking orbit orientation (RAAN and argument of perigee) and TLI magnitude to simultaneously achieve the target perilune altitude and free-return geometry.

## Mission Profile

**Note on launch dates**: The launch epoch in these scripts (February 2026) reflects an earlier planned launch window used during development. The actual Artemis II launch occurred in April 2026. The trajectory geometry and delta-V budget are representative of the mission profile regardless of the specific launch date; only the parking orbit orientation (RAAN/AOP) changes to align with the Moon's position at the time of departure.

The simulation models the following mission timeline:

1. **Parking orbit coast** (~1 hour) -- 185 km circular orbit, 28.5-degree inclination from KSC
2. **Trans-Lunar Injection** (~T+1:00) -- Single impulsive burn of ~3.15 km/s
3. **Trans-lunar coast** (~4 days) -- Passive coast to the Moon
4. **Lunar far-side flyby** (~Day 4) -- ~4,800 km altitude pass, no orbit insertion
5. **Free-return arc** (~4 days) -- Passive return with no mid-course corrections
6. **Entry interface** (~Day 8) -- ~11 km/s at ~120 km altitude, Pacific splashdown

### Converged Results

| Parameter | Value |
|-----------|-------|
| TLI delta-V | 3.1485 km/s |
| Perilune RMAG | 6,538 km (4,800 km altitude) |
| Return perigee | 6,499 km (121 km altitude) |
| Entry speed | 11.0 km/s |
| Mission duration | 7.6 days |

## Scripts

### `artemis2_freereturn.script` -- Targeting Script

The primary script. Uses a two-phase differential corrector to converge on the free-return trajectory:

- **Phase 1 (Coarse)**: Uses a simplified point-mass Earth model to quickly orient the departure trajectory toward the Moon by varying RAAN and argument of perigee. Targets RA = 0 and DEC = 0 in the Earth-Moon rotating frame.
- **Phase 2 (Fine)**: Resets to initial conditions and switches to the full force model. Varies RAAN, AOP, and TLI delta-V magnitude to simultaneously achieve the target perilune distance (~6,537 km from lunar center) and return perigee (~6,438 km from Earth center).

The solver converges in approximately 63 iterations.

### `artemis2_playback.script` -- Converged Solution Playback

Replays the converged trajectory solution with hardcoded orbital elements and burn magnitude. No solver iterations -- this script runs the final trajectory with full visualization:

- Three orbit views: Earth-centered inertial, Moon close-up, Earth-Moon rotating frame
- Ground track plot (Mercator projection)
- CSV trajectory report at key mission events

The hardcoded values (RAAN = 7.029 deg, AOP = 86.598 deg, TLI = 3.1485 km/s) are the converged output from the targeting script.

## Technical Details

### Force Models

| Model | Gravity | Third-Body | SRP | Usage |
|-------|---------|-----------|-----|-------|
| EarthFull | JGM2 10x10 | Sun, Moon, Venus, Mars, Jupiter | Spherical, 1367 W/m^2 | Primary Earth propagation |
| MoonFull | LP165P 10x10 | Sun, Earth, Jupiter | Spherical, 1367 W/m^2 | Lunar vicinity |
| EarthPointMass | Point mass | None | Off | Coarse targeting phase |

### Integrator

Runge-Kutta 8/9 with 1e-12 accuracy tolerance across all propagators.

### Spacecraft Model

- **Mass**: 26,500 kg (Orion CM + ESM at separation)
- **Drag coefficient**: 2.2 (Cd), drag area 20 m^2
- **Reflectivity**: 1.8 (Cr), SRP area 22 m^2

### Key Assumptions

- **No ascent modeling** -- the simulation starts with Orion in the parking orbit
- **Impulsive burns** -- instantaneous velocity changes rather than finite-duration thrust arcs
- **No propellant mass decrement** -- spacecraft mass stays constant throughout
- **No atmospheric drag** -- negligible at 185 km over the ~1 hour parking orbit coast
- **No mid-course corrections** -- the free-return trajectory is entirely passive after TLI

These are standard assumptions for preliminary trajectory design. The trajectory geometry and delta-V budget are well-captured by this approach; a mission-fidelity simulation would add finite burns, propellant bookkeeping, navigation errors, and correction maneuvers.

## Requirements

### GMAT R2025a

This simulation was developed and tested with **NASA GMAT R2025a**.

- **Download**: [GMAT R2025a on SourceForge](https://sourceforge.net/projects/gmat/files/GMAT/GMAT-R2025a/)
- **NASA Software Catalog**: [GSC-19468-1](https://software.nasa.gov/software/GSC-19468-1)
- **Documentation**: [GMAT Wiki](https://gmat.atlassian.net/wiki/)

GMAT is an open-source flight dynamics tool developed by NASA Goddard Space Flight Center. It is available for Windows, macOS, and Linux.

### Running the Scripts

1. Install GMAT R2025a
2. Open any `.script` file in the GMAT GUI, or run from the command line:
   ```
   GMAT artemis2_freereturn.script
   ```
3. The targeting script will run the differential corrector and display solver progress. The playback script runs without iteration.

**Recommended order**:
- Run `artemis2_freereturn.script` first to see the targeting process converge
- Run `artemis2_playback.script` for the final trajectory visualization

## Data Sources

The mission parameters used in this simulation are derived from publicly available NASA sources:

- **Artemis II Mission Page**: [nasa.gov/mission/artemis-ii](https://www.nasa.gov/mission/artemis-ii/) -- Mission overview, timeline, and trajectory description
- **Artemis II Press Kit**: [nasa.gov/artemis-ii-press-kit](https://www.nasa.gov/artemis-ii-press-kit/) -- Detailed mission profile, orbital altitudes, and mission duration
- **NASA SVS Reference Trajectory**: [svs.gsfc.nasa.gov/5610](https://svs.gsfc.nasa.gov/5610/) -- Nominal Artemis II trajectory visualization from the Scientific Visualization Studio
- **Orion Spacecraft**: Mass and physical parameters are consistent with NASA's published Orion specifications (~26,500 kg CM+ESM at upper stage separation)
- **KSC Launch Parameters**: The 28.5-degree inclination is the standard constraint for launches from Kennedy Space Center. The 185 km parking orbit altitude is consistent with SLS mission planning documentation available through the [NASA Technical Reports Server (NTRS)](https://ntrs.nasa.gov/)

**Note**: This is an independent simulation for educational and analytical purposes. It is not affiliated with or endorsed by NASA. The trajectory is representative of the publicly described mission profile but does not use proprietary NASA flight dynamics data.

## Citation

If you use or reference this simulation in academic work, publications, or professional projects:

> Nixon, M. (2026). Artemis II Free-Return Trajectory Simulation. OrbitLink Consulting. https://orbitlink.org

## License

This project is released under the [BSD 3-Clause License](LICENSE).

Copyright 2026 OrbitLink Consulting, LLC.
