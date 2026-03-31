# CircuitVerse Template Library

A comprehensive collection of reusable digital circuit templates for CircuitVerse, designed to accelerate circuit design, learning, and prototyping.

## Overview

The Template Library provides pre-built, configurable circuit templates covering fundamental digital logic components. Each template includes:

- **JSON Configuration**: Machine-readable circuit definition for programmatic instantiation
- **SVG Diagram**: Visual representation of the circuit topology
- **Tutorial Documentation**: Step-by-step explanation of circuit operation

## Directory Structure

```
templates/
|-- TEMPLATE_LIBRARY.md      # This documentation file
|-- diagrams/
|   |-- half_adder.svg       # Half Adder circuit diagram
|   |-- full_adder.svg       # Full Adder circuit diagram
|   |-- sr_flipflop.svg      # SR Flip-Flop diagram
|-- half_adder.json          # Half Adder template
|-- full_adder.json          # Full Adder template
|-- sr_flipflop.json         # SR Flip-Flop template
|-- multiplexer.json         # 4-to-1 Multiplexer template
|-- binary_counter.json      # 4-Bit Binary Counter template
```

## Available Templates

### 1. Half Adder

**File**: `half_adder.json` | **Diagram**: `diagrams/half_adder.svg`

A basic arithmetic circuit that adds two single-bit binary numbers.

| Property | Value |
|----------|-------|
| **Inputs** | A, B (1-bit each) |
| **Outputs** | Sum, Carry |
| **Gates Used** | 1 XOR, 1 AND |
| **Difficulty** | Beginner |
| **Category** | Arithmetic |

**Boolean Expressions**:
- Sum = A XOR B
- Carry = A AND B

**Use Cases**: Foundation for multi-bit adders, ALU design, binary arithmetic units.

---

### 2. Full Adder

**File**: `full_adder.json` | **Diagram**: `diagrams/full_adder.svg`

A combinational circuit that adds three single-bit binary numbers (two operands + carry-in).

| Property | Value |
|----------|-------|
| **Inputs** | A, B, Cin (1-bit each) |
| **Outputs** | Sum, Cout |
| **Gates Used** | 2 XOR, 2 AND, 1 OR |
| **Difficulty** | Beginner |
| **Category** | Arithmetic |

**Boolean Expressions**:
- Sum = A XOR B XOR Cin
- Cout = (A AND B) OR (Cin AND (A XOR B))

**Use Cases**: Ripple carry adders, multi-bit addition, ALU implementation.

---

### 3. SR Flip-Flop

**File**: `sr_flipflop.json` | **Diagram**: `diagrams/sr_flipflop.svg`

A basic sequential storage element (Set-Reset latch) built using NOR gates.

| Property | Value |
|----------|-------|
| **Inputs** | S (Set), R (Reset), CLK |
| **Outputs** | Q, Q' (complement) |
| **Gates Used** | 2 NOR, 2 AND |
| **Difficulty** | Intermediate |
| **Category** | Sequential / Storage |

**Truth Table**:
| S | R | Q(next) | Description |
|---|---|---------|-------------|
| 0 | 0 | Q(prev) | Hold |
| 0 | 1 | 0 | Reset |
| 1 | 0 | 1 | Set |
| 1 | 1 | Invalid | Forbidden |

**Use Cases**: Memory elements, registers, state machines, counters.

---

### 4. 4-to-1 Multiplexer

**File**: `multiplexer.json`

A combinational circuit that selects one of four input signals based on select lines.

| Property | Value |
|----------|-------|
| **Inputs** | I0, I1, I2, I3 (data), S0, S1 (select) |
| **Outputs** | Y (selected output) |
| **Gates Used** | 4 AND, 1 OR, 2 NOT |
| **Difficulty** | Intermediate |
| **Category** | Combinational |

**Boolean Expression**:
- Y = (S1' AND S0' AND I0) OR (S1' AND S0 AND I1) OR (S1 AND S0' AND I2) OR (S1 AND S0 AND I3)

**Use Cases**: Data routing, signal selection, bus systems, ALU function selection.

---

### 5. 4-Bit Binary Counter

**File**: `binary_counter.json`

A sequential circuit that counts from 0 to 15 in binary using T flip-flops.

| Property | Value |
|----------|-------|
| **Inputs** | CLK, RESET |
| **Outputs** | Q3, Q2, Q1, Q0 (4-bit count) |
| **Gates Used** | 4 T Flip-Flops |
| **Difficulty** | Intermediate |
| **Category** | Sequential / Counter |

**Operation**: On each clock pulse, the counter increments by 1. The RESET input clears all outputs to 0.

**Use Cases**: Timing circuits, frequency dividers, sequential control, digital clocks.

---

## Template Schema

All JSON templates follow a standardized schema for CircuitVerse compatibility:

```json
{
  "name": "Template Name",
  "description": "Brief description of the circuit",
  "category": "arithmetic|sequential|combinational",
  "difficulty": "beginner|intermediate|advanced",
  "inputs": [
    { "id": "input_id", "label": "A", "type": "digital" }
  ],
  "outputs": [
    { "id": "output_id", "label": "Sum", "type": "digital" }
  ],
  "components": [
    {
      "type": "XOR",
      "id": "xor1",
      "inputs": ["input_id"],
      "position": { "x": 100, "y": 50 }
    }
  ],
  "connections": [
    { "from": "xor1/output", "to": "output_id/input" }
  ],
  "tutorial": {
    "steps": [
      "Step-by-step explanation..."
    ]
  }
}
```

## How to Use Templates

### In CircuitVerse

1. Navigate to the CircuitVerse project editor
2. Use the template import feature (if available)
3. Load the desired `.json` template file
4. Configure inputs as needed
5. Simulate and observe outputs

### Programmatically

```javascript
// Example: Loading a template
async function loadTemplate(templatePath) {
  const response = await fetch(templatePath);
  const template = await response.json();
  // Instantiate circuit from template definition
  return CircuitVerse.importCircuit(template);
}
```

## Extending the Library

To add a new template:

1. **Design the circuit** in CircuitVerse or draw it manually
2. **Create the JSON file** following the schema above
3. **Generate an SVG diagram** (see `diagrams/` for examples)
4. **Write documentation** for the template section above
5. **Test** the template in CircuitVerse before committing

## Contributing

Contributions are welcome! When submitting a new template:

- Follow the established JSON schema
- Include clear comments in the configuration
- Provide an SVG diagram in `diagrams/`
- Document truth tables, boolean expressions, and use cases
- Test thoroughly before submission

## License

This template library is released under the MIT License (see root LICENSE file).

---

*Generated as part of GSoC 2026 preparation for CircuitVerse*
