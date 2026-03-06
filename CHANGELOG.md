# Changelog

All notable changes to the SOHA Module Registry will be documented in this file.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [v0.1.3] - 2026-03-06

### Added
- **drive**: `soha:physics.joint.limits` — maxVelocity (20.94 rad/s), maxEffort (2.0 Nm)
- **drive**: `status.velocity` — estimated speed from back-EMF (rad/s)
- **drive**: `stall` event — back-EMF detects motor stalled or heavily loaded
- **drive**: `overcurrent` event — motor current exceeds safe threshold
- **servo**: `targetReached` event — angle reached within deadband after setTarget
- **servo**: `stall` event — motor current indicates stall (gripper contact detection)
- **joint**: `targetReached` event — angle reached within deadband, includes remaining error
- **joint**: `stall` event — torque limit reached while position error remains
- **battery**: `status.capacity` — battery total capacity (mAh)
- **battery**: `overTemperature` event — cell temperature exceeds safe operating range
- **display**: `status.current_expression` — active expression name or 'none'
- **controller**: `joystickMoved` event — fires when position exceeds deadzone threshold
- **developer**: `gpioInterrupt` event — input pin state changed (rising/falling edge)

### Changed
- **drive**: description updated to mention back-EMF speed estimation
- **STANDARD.md**: `continuous` joint type now requires `limits` (maxVelocity, maxEffort); added `soha:moduleName` as required field
- All module READMEs updated to reflect new properties/events

## [v0.1.2] - 2026-03-05

### Added
- `soha:moduleName` identifier to all 10 modules for programmatic registry lookup

## [v0.1.1] - 2026-03-04

### Fixed
- **joint**: removed incorrect reducer/gearbox references; joint is direct-drive

## [v0.1.0] - 2026-03-04

### Added
- Initial SOHA module registry with 10 modules:
  - **Actuators**: drive, servo, joint
  - **Peripherals**: battery, controller, display, led, switch, voice, developer
- WoT Thing Model (TM) definition for each module (`.tm.json`)
- WoT Thing Description (TD) instance for each module (`.td.json`)
- `soha:physics` extension for simulation-ready joint/actuator parameters

### Fixed
- **joint**: joint type changed to `continuous` for unlimited rotation
- **servo**: joint type changed to `continuous` for unlimited rotation
