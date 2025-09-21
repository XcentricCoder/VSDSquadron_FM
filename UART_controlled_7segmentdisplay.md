# UART-Controlled 7-Segment Display System with VSD Squadron FPGA Mini

## Project Overview
This project implements a UART communication system that controls  7-segment displays using the VSD Squadron Mini FPGA board. The system receives data via serial communication and visually displays it through the connected output devices.

## Hardware Requirements

### Essential Components
1. **VSD Squadron Mini FPGA Board**
   - Lattice iCE40UP5K FPGA
   - Built-in USB-to-UART (FTDI) interface
   - 12 MHz onboard clock
   - GPIO headers for external connections

2. **Display Options (Choose One)**
   - **7-Segment Display**: Common anode or cathode type with 7x 220Ω-330Ω resistors
   - **4-Digit 7-Segment Display**: Additional components needed:
     - 4x NPN transistors (2N3904) or P-Channel MOSFETs (IRF9540)
     - 4x 1kΩ-10kΩ base/gate resistors

3. **Connection & Prototyping**
   - Breadboard for easy prototyping
   - Jumper wires (Male-to-Male recommended)
   - USB-C cable for power and programming

### Pin Connections

#### UART Interface
| Signal | Squadron Mini Pin | Direction | Description |
|--------|-------------------|-----------|-------------|
| RX     | FTDI_TX (from docs) | FPGA Input | Serial data from PC |
| TX     | FTDI_RX (from docs) | FPGA Output | Serial data to PC (optional) |
| GND    | Any GND pin       | -         | Common ground |


#### 7-Segment Display Connection (Common Cathode)
| FPGA Signal | GPIO Pin Example | Connection |
|-------------|------------------|------------|
| segment_a   | GPIO_10          | Segment A through resistor |
| segment_b   | GPIO_11          | Segment B through resistor |
| ...         | ...              | ... |
| segment_g   | GPIO_16          | Segment G through resistor |
| GND         | Any GND pin      | Common cathode pin |

*Note: For common anode displays, connect common anode to 3.3V and drive segments LOW to activate.*

## Implementation Details

### System Architecture
```
PC/UART Terminal → FTDI Chip → UART Receiver → Output Controller → LEDs/7-Segment Display
```

### Key Modules

1. **UART Receiver**
   - Baud rate: 115200 (generated from 12 MHz clock)
   - Data format: 8 data bits, no parity, 1 stop bit
   - Outputs: 8-bit parallel data + data valid signal

2. **Output Controller**
   - For LEDs: Direct mapping of received bytes to LED pins
   - For 7-Segment: ASCII to 7-segment decoder using lookup table

```
   `timescale 1ns / 1ps

module binary_to_7segment(
    input [3:0] bin_input,     // 4-bit binary input
    output reg [6:0] seg_output // 7-segment output (a, b, c, d, e, f, g)
);

// Segment encoding for common cathode display
// Format: {a, b, c, d, e, f, g}

always @(*) begin
    case (bin_input)
        4'b0000: seg_output = 7'b0111111; // 0
        4'b0001: seg_output = 7'b0000110; // 1
        4'b0010: seg_output = 7'b1011011; // 2
        4'b0011: seg_output = 7'b1001111; // 3
        4'b0100: seg_output = 7'b1100110; // 4
        4'b0101: seg_output = 7'b1101101; // 5
        4'b0110: seg_output = 7'b1111101; // 6
        4'b0111: seg_output = 7'b0000111; // 7
        4'b1000: seg_output = 7'b1111111; // 8
        4'b1001: seg_output = 7'b1101111; // 9
        4'b1010: seg_output = 7'b1110111; // A
        4'b1011: seg_output = 7'b1111100; // B
        4'b1100: seg_output = 7'b0111001; // C
        4'b1101: seg_output = 7'b1011110; // D
        4'b1110: seg_output = 7'b1111001; // E
        4'b1111: seg_output = 7'b1110001; // F
        default: seg_output = 7'b0000000; // Default off
    endcase
end

endmodule
```

3. **Top-Level Module**
   - Instantiates UART receiver and output controller
   - Maps physical pins to internal signals
  
   

### Resource Utilization (Estimate)
- **Logic Elements**: < 200 LUTs/FFs
- **Memory**: Minimal (no BRAM required for basic implementation)
- **I/O Pins**: 8-16 for displays + 2 for UART

## Setup Instructions

1. **Hardware Assembly**
   - Connect display components to breadboard with appropriate resistors
   - Wire display to Squadron Mini GPIO headers according to pinout plan
   - Connect USB-C cable for power and communication

2. **Software Setup**
   - Install open-source FPGA toolchain (Yosys, NextPnR, IceStorm)
   - Prepare terminal software (PuTTY, Tera Term, or screen)

3. **Development Process**
   - Create pin constraint file (.pcf) with correct pin assignments
   - Implement and test UART receiver module
   - Implement output controller for chosen display
   - Synthesize, place-and-route, and program the FPGA
```
set_io bcd[0] 42
set_io bcd[1] 43
set_io bcd[2] 44
set_io bcd[3] 45
set_io seg[0] 46
set_io seg[1] 13
set_io seg[2] 48
set_io seg[3] 3
set_io seg[4] 4
set_io seg[5] 12
set_io seg[6] 26
```

## Usage Example

1. **For 7-Segment Display**:
   - Send ASCII characters to display
   - Example: Sending '5' will display the numeral 5

## Troubleshooting Tips

- Verify voltage levels (3.3V for all GPIO)
- Confirm correct baud rate (115200) in terminal software
- Check all connections for continuity
- Verify resistor values to prevent LED damage
- For 4-digit displays, ensure proper multiplexing timing

## Expansion Ideas

- Add UART transmit capability for bidirectional communication
- Implement scrolling text on multiple 7-segment displays
- Add control commands for different display modes
- Interface with multiple display types simultaneously

## References

- VSD Squadron Mini documentation and pinout diagram
- iCE40UP5K Datasheet
- FTDI FT2232H Datasheet (for UART interface details)
- Display component datasheets (for pinouts and electrical characteristics)

## ![Recording](https://github.com/XcentricCoder/VSDSquadron_FM/blob/main/display_recording.mp4)

This project provides a foundation for understanding UART communication and FPGA-based display control, with minimal hardware requirements focused on the VSD Squadron Mini's capabilities.
