# 📡 RFSoC SDR Modulation Lab

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Vivado Version](https://img.shields.io/badge/Vivado-2022.2-blue)](https://www.xilinx.com/support/download.html)
[![PYNQ Version](https://img.shields.io/badge/PYNQ-3.0.1-orange)](http://www.pynq.io/)
[![Board](https://img.shields.io/badge/Board-RFSoC%204x2-green)](https://www.rfsoc-pynq.io/)

An open-source, modular repository for exploring, implementing, and testing various RF modulation techniques on the **Xilinx Zynq UltraScale+ RFSoC 4x2** development board. 

This repository acts as a "playground" for direct-RF sampling, utilizing the RFSoC's integrated ADCs/DACs and the PYNQ framework to bridge the gap between high-level Python algorithms and low-level FPGA RTL.

---

## 📋 Table of Contents
- [Overview](#overview)
- [Hardware & Software Requirements](#hardware--software-requirements)
- [Repository Structure](#repository-structure)
- [Project Roadmap](#project-roadmap)
- [Quick Start Guide (SSB AM)](#quick-start-guide-ssb-am)
- [Adding a New Modulation Project](#adding-a-new-modulation-project)
- [Contributing](#contributing)
- [License](#license)

---

## 🌟 Overview

Traditional SDRs rely on external mixers and ADCs. The RFSoC architecture integrates the RF data converters (RFDC) directly into the FPGA fabric, allowing for direct RF sampling and generation. 

This repository breaks down complex modulation schemes into isolated, reproducible projects. Each project contains:
1. **Hardware (PL):** Custom RTL (Verilog/VHDL) and Vivado Block Designs for the modulation/demodulation logic.
2. **Software (PS):** PYNQ Jupyter Notebooks for RFDC configuration, data streaming, and real-time spectrum analysis.
3. **Documentation:** Theory of operation, link budgets, and architecture diagrams.

---

## 🛠 Hardware & Software Requirements

### Hardware
*   **Primary Board:** [Zynq UltraScale+ RFSoC 4x2](https://www.rfsoc-pynq.io/) (Rev A or Rev B).
*   **Audio Interface (For SSB/AM):** [Audio Codec PMOD](https://digilent.com/shop/pmod-audio-codec-with-line-in-and-headphone-jack/) or similar I2S audio PMOD connected to the appropriate header. *(Note: The 4x2 baseboard does not have native audio jacks).*
*   **RF Cables & Antennas:** SMA cables for loopback testing or transmitting/receiving via antennas (ensure compliance with local RF transmission laws!).

### Software
*   **Xilinx Vivado:** Version **2022.2** (or newer). *Ensure you have the RFSoC device support installed.*
*   **PYNQ Image:** RFSoC 4x2 PYNQ SD Card Image (v3.0.1 or newer).
*   **Python Environment:** Python 3.10+ with `pynq`, `numpy`, `scipy`, and `matplotlib` installed on the board.

---

## 📂 Repository Structure

This is a monorepo designed to share common IP cores while keeping specific modulation implementations isolated.

```text
rfsoc-sdr-modulation-lab/
├── common/               # Shared IP cores (Hilbert, NCO), PYNQ utilities, and constraints
├── projects/             # Individual modulation projects
│   ├── ssb_am_rfsoc4x2/  # Single Sideband AM (Current)
│   ├── bpsk_rfsoc4x2/    # Binary Phase Shift Keying (Planned)
│   └── ofdm_rfsoc4x2/    # Orthogonal Frequency Division Multiplexing (Planned)
├── .github/              # CI/CD workflows and issue templates
└── README.md             # You are here!
```

---

## 🗺 Project Roadmap

| Project | Modulation Type | Status | Description |
| :--- | :--- | :---: | :--- |
| **SSB AM** | Analog (Single Sideband) | 🟢 **Active** | Voice modulation using Hilbert transform for sideband suppression. |
| **BPSK** | Digital (Phase Shift) | 🟡 Planned | Basic digital modulation, implementing matched filtering and clock recovery. |
| **QPSK** | Digital (Phase Shift) | ⚪ Backlog | 4-state phase modulation, introducing I/Q baseband processing. |
| **OFDM** | Digital (Multicarrier) | ⚪ Backlog | Complex multicarrier modulation using FFT/IFFT and cyclic prefixes. |

---

## 🚀 Quick Start Guide (SSB AM)

### 1. Clone the Repository
```bash
git clone https://github.com/[YourUsername]/rfsoc-sdr-modulation-lab.git
cd rfsoc-sdr-modulation-lab/projects/ssb_am_rfsoc4x2
```

### 2. Build the Hardware (Vivado)
Open Vivado and run the TCL script to generate the block design and bitstream:
```tcl
# In Vivado Tcl Console
source hardware/scripts/make_project.tcl
source hardware/scripts/build_bitstream.tcl
```
*This will generate the `.bit` and `.hwh` files in the `hardware/` directory.*

### 3. Deploy to RFSoC 4x2
Copy the generated overlay files and the PYNQ notebooks to your RFSoC board:
```bash
# From your host PC
scp hardware/ssb_am.bit xilinx@<rfsoc-ip>:/home/xilinx/pynq/overlays/ssb_am/
scp hardware/ssb_am.hwh xilinx@<rfsoc-ip>:/home/xilinx/pynq/overlays/ssb_am/
scp -r software/pynq/ xilinx@<rfsoc-ip>:/home/xilinx/jupyter_notebooks/ssb_am/
```

### 4. Run the Jupyter Notebook
1. Open your browser and navigate to `http://<rfsoc-ip>:9090` (PYNQ Jupyter).
2. Open the `ssb_am_tx.ipynb` (for transmitting) or `ssb_am_rx.ipynb` (for receiving).
3. Follow the step-by-step instructions in the notebook to configure the RFDC and stream audio!

---

## ➕ Adding a New Modulation Project

Want to add QPSK or FSK? Follow these steps to maintain repo consistency:

1. Create a new folder under `projects/` (e.g., `qpsk_rfsoc4x2/`).
2. Copy the internal folder structure (`docs/`, `hardware/`, `software/`) from the `ssb_am_rfsoc4x2` project.
3. Add your new custom IP to `common/ip_repo/` if it's reusable (e.g., a new mapper).
4. Update the **Project Roadmap** table in this README.
5. Submit a Pull Request!

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! 
If you find a bug in the RTL or have an optimization for the PYNQ Python code, please open an issue or submit a PR. 

When submitting hardware changes, please ensure your Vivado project builds cleanly from the TCL scripts without requiring manual GUI intervention.

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

*   **Xilinx/AMD** for the RFSoC architecture and PYNQ framework.
*   **RealDigital / RFSoC 4x2 Team** for the excellent board support packages and documentation.
