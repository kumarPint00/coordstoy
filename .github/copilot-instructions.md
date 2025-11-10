# Toy Robot Simulator - AI Development Guide

## Architecture Overview

This is a **clean architecture implementation** of a toy robot simulator with clear separation of concerns:

- **Core Domain** (`toyRobotSimulation.ts`): Stateful simulation engine with boundary validation
- **Value Objects** (`position.ts`, `orientation.ts`): Immutable data structures and pure functions  
- **Command Interface** (`commandAdaptor.ts`): Command pattern implementation with dependency injection
- **CLI Entry Point** (`index.ts`): Interactive readline interface

The **data flow** follows: CLI input → Command Adaptor → Simulation Core → Position/Orientation utilities.

## Key Design Patterns

### Functional Programming with Classes
- **Pure functions** for orientation logic (`left()`, `right()`, `forwardMovement()`)
- **Immutable updates** using spread syntax: `{ ...this.robot, orientation: left(this.robot.orientation) }`
- **Boundary validation** through `isPositionValid()` prevents invalid state mutations

### Command Pattern with Dependency Injection
```typescript
// Always inject dependencies into adaptors
const trsca = toyRobotSimulationCommandAdaptor(new ToyRobotSimulation(), console.log)
```

### Enum-Based State Management
- Use `Orientation` enum for directional logic, not string literals
- Access enum values via `Orientation[match[3] as keyof typeof Orientation]`

## Testing Conventions

### Test Structure Pattern
- **Helper functions** at bottom of test files (`originPosition()`, `fiveByFiveTable()`)
- **Boundary testing**: Always test edge cases (negative coords, table boundaries)
- **Integration tests** in `commandAdaptor.test.ts` using README examples

### Test Naming Convention
```typescript
test("should [action] [expected behavior]", () => {
  // Arrange, Act, Assert pattern
})
```

## Development Workflows

### Running Commands
```bash
npm install          # Install dependencies
npm run test         # Run Jest test suite
npm run start        # Interactive CLI mode
```

### TypeScript Configuration
- **Strict mode enabled** - all code must satisfy strict type checking
- **No emit compilation** - runtime uses `ts-node` directly
- Target ES2016 with CommonJS modules

## Project-Specific Conventions

### Robot State Management
- Robot starts as `undefined` until valid PLACE command
- **Silent failure pattern**: Invalid moves/commands are ignored, not errored
- Always check `this.robot` exists before operations

### Coordinate System
- Origin (0,0) at **SOUTH WEST** corner (different from typical programming conventions)
- Y-axis increases **NORTH**, X-axis increases **EAST**
- Default table size: 5x5 (configurable via constructor)

### Command Processing
- Use regex matching for PLACE commands: `/PLACE (\d+),(\d+),(NORTH|EAST|SOUTH|WEST)/`
- Command parsing is **case-sensitive** and **whitespace-sensitive**
- Return early from switch statements for command handling

### File Organization
- Keep domain logic (`toyRobotSimulation.ts`) separate from I/O concerns
- Co-locate tests with source files using `.test.ts` suffix  
- Use barrel exports pattern if adding more modules