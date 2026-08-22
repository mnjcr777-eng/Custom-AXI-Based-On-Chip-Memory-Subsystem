This document maps the repository files to their roles in the Vivado-based FPGA workflow. It explains how the custom AXI IP is packaged and added to the block design, how memory images initialize the BRAM-based subsystems for simulation or hardware tests, and how constraints and Tcl scripts prepare the design for implementation on the target FPGA board.

Here is a brief but complete breakdown of the purpose of each file and exactly where it is used in your Vivado project workflow:

### 1. Custom IP Source & Packaging Files
*   AT1051_CDAC.v
    *   Purpose: The core hardware description (Verilog code) defining the logic of your custom AXI Master (AT1051_CDAC_0).
    *   Where it is used: Imported into Vivado as a design source file.
*   component.xml
    *   Purpose: The metadata file that tells Vivado how to package your Verilog code into a standardized IP block (defining the AXI interfaces, ports, and GUI parameters).
    *   Where it is used: Read automatically by the Vivado IP Catalog when you add your custom IP repository to a project.
*   AT1051_CDAC_v1_0.tcl
    *   Purpose: A Tcl script that configures how the IP looks and behaves in the Vivado GUI (handling parameters and IP integration).
    *   Where it is used: Executed internally by Vivado when placing the AT1051_CDAC_0 block into your Block Design.

### 2. Memory Initialization Files (COE & BIN)
*   boot.coe
    *   Purpose: Preloads the memory with boot-up instructions or startup data.
    *   Where it is used: Loaded via the "Load Init File" option in the blk_mem_gen IP configuration GUI.
*   GPIO_sim.coe & UART_sim.coe
    *   Purpose: Preloads the memory with test vectors to simulate GPIO and UART traffic without needing real hardware.
    *   Where it is used: Loaded into a blk_mem_gen block specifically when running Vivado behavioral simulations.
*   GPIO_toggle.coe
    *   Purpose: Preloads a specific data pattern designed to repeatedly toggle/blink the external GPIO pins.
    *   Where it is used: Loaded into a blk_mem_gen block prior to generating a bitstream for real-world hardware testing.
*   GPIO_toggle.bin
    *   Purpose: The raw binary equivalent of the toggle COE file.
    *   Where it is used: Used by software bootloaders (like Xilinx SDK/Vitis) to load the pattern into memory dynamically at run-time, rather than baking it into the bitstream.

### 3. Constraints & Setup Scripts
*   AT1051.xdc
    *   Purpose: The constraints file that maps the logical ports in your design (like `gpio_io`) to the physical pins on your specific FPGA board, and sets their voltage levels.
    *   Where it is used: Added as a Constraints source file in the Vivado project manager.
*   pre_tcl.tcl
    *   Purpose: A Tcl script used to automate tasks (like setting up the Vivado project, building the block design, or preparing environment variables).
    *   Where it is used: Run manually in the Vivado Tcl Console at the start of the project, or configured as a pre-synthesis hook in the project settings.
