# VSD FPGA Mini (FM) Board

The **VSDSquadron FPGA Mini (FM)** is a compact, cost-effective FPGA development board designed for learning, prototyping, and exploring digital logic, VLSI, and embedded systems. It is based on the **Lattice iCE40UP5K** FPGA and supports the fully open-source FPGA toolchain.

---

## Board Specifications

### Core FPGA
- **Chip**: Lattice iCE40UP5K-SG48ITR  
- **Logic Resources**: ~5.3K LUTs  
- **On-Chip RAM**:
  - 1 Mb SPRAM (Single-Port SRAM)  
  - 120 Kb DPRAM (Dual-Port RAM)  
- **DSP Resources**: 8 × hardware multipliers  

---

### Memory & Storage
- **Non-volatile Storage**: 4 MB SPI flash (for configuration + data)  

---

### Programming & Connectivity
- **USB Bridge**: FTDI FT232H  
  - Provides USB-to-SPI for FPGA programming  
  - Supports serial communication  
- **GPIO Access**: 32 accessible FPGA GPIO pins  

---

### Indicators
- **RGB LED**: Onboard RGB LED with dedicated PWM + current control via FPGA primitive  

---

### Power & Voltage
- **Onboard Regulators**:
  - 3.3 V  
  - 1.2 V (FPGA core)  
- **I/O Voltage Levels Supported**: 1.8 V / 2.5 V / 3.3 V  
- **External Power Input**: Supported  

---

### Clocking
- **Internal Oscillator**: Built-in configurable clock source  
- **External Clock Input**: Supported  
- **Max Frequency**: ~133 MHz  

---

### Physical Specifications
- **Form Factor**: 57 mm × 29 mm  
- **Operating Temperature**: –40 °C to 85 °C  

---

### Development Ecosystem
- **Toolchain Compatibility**:  
  - Project IceStorm (bitstream generation)  
  - Yosys (synthesis)  
  - nextpnr (place & route)  

- **Ideal For**:  
  - Students learning digital design and FPGA fundamentals  
  - Makers and researchers experimenting with open-source silicon  
  - Hardware prototyping and VLSI education  

---

## Summary Table

| Feature            | Details                        |
|--------------------|--------------------------------|
| FPGA               | Lattice iCE40UP5K (5.3K LUTs)  |
| RAM                | 1 Mb SPRAM + 120 Kb DPRAM      |
| DSP                | 8 Multipliers                  |
| Flash              | 4 MB SPI                       |
| GPIOs              | 32                             |
| USB Bridge         | FT232H (USB-SPI, Serial)       |
| Indicators         | 1 × RGB LED                    |
| Power Rails        | 1.2 V, 3.3 V (onboard regs)    |
| Size               | 57 mm × 29 mm                  |
| Max Frequency      | 133 MHz                        |
| Toolchain          | Yosys + nextpnr + IceStorm     |

---

## References
- [VSD FPGA Mini (FM) Datasheet](https://www.vlsisystemdesign.com/vsdsquadronfm/)  
- [Open-Source FPGA Toolchain](http://www.clifford.at/icestorm/)  

