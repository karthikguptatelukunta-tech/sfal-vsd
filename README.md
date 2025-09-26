# sfal-vsd
<details>
<summary>Day 0 - Tools Installation</summary>

### Yosys

```bash
$ git clone https://github.com/YosysHQ/yosys.git
$ cd yosys
$ sudo apt install make   # (If make is not installed please install it)
$ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
$ make
$ sudo make install
```

[![Image](https://github.com/user-attachments/assets/5a9aeb0d-8f95-444a-b6ae-e945320eb726)](https://github.com/karthikguptatelukunta-tech/sfal-vsd/issues/1#issue-3436974372)



### iverilog
```bash
$ sudo apt-get install iverilog
```
![iverilog](https://github.com/user-attachments/assets/1e5f143e-8087-48c4-bddf-82255f542e38)

###Gtkwave
```bash
$ sudo apt update
$ sudo apt install gtkwave
```
![WhatsApp Image 2025-09-19 at 23 33 45](https://github.com/user-attachments/assets/61431f8d-6f14-453f-b52f-5f36052e50f1)
![WhatsApp Image 2025-09-19 at 23 33 45 (1)](https://github.com/user-attachments/assets/03ecd167-3d8b-45e6-828c-df115ab34e68)
</details>
<details>
	<summary>Day 1 - Introduction to Verilog RTL Design and Synthesis </summary>

# Day 1 - Introduction to Verilog RTL Design and Synthesis
## Introduction to open-source simulator Iverilog

Folder structure of the git clone:
- `lib` - will contain sky130 standard cell library
- `my_lib/verilog_models` - will contain standard cell verilog model
- `verilog_files` -contains the lab experiments source files

<img width="1541" height="784" alt="Screenshot 2025-09-25 151227" src="https://github.com/user-attachments/assets/c4a5d59f-71f6-4c5c-b194-437c7ac91376" />
<img width="1904" height="1018" alt="Screenshot 2025-09-25 151402" src="https://github.com/user-attachments/assets/6f42da14-0842-44fd-a97f-ac39cc153a69" />



Example of a design good_mux.v 

```
module good_mux (input i0 , input i1 , input sel , output reg y);
always @ (*)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
Example of a testbench tb_good_mux.v 

```
`timescale 1ns / 1ps
module tb_good_mux;
	// Inputs
	reg i0,i1,sel;
	// Outputs
	wire y;

        // Instantiate the Unit Under Test (UUT)
	good_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);

	initial begin
	$dumpfile("tb_good_mux.vcd");
	$dumpvars(0,tb_good_mux);
	// Initialize Inputs
	sel = 0;
	i0 = 0;
	i1 = 0;
	#300 $finish;
	end

always #75 sel = ~sel;
always #10 i0 = ~i0;
always #55 i1 = ~i1;
endmodule
```
Command to run the design and testbench
```
iverilog good_mux.v tb_good_mux.v
```
The output of the iverilog is a .vcd file and a.out file is created. By executing a.out iverilog dump the vcd file.

## Introduction to GTKWave
gtkwave will be used to generate the waveforms and display in visual format.

Command to view the vcd file in gtkwave 
```
gtkwave tb_good_mux.vcd
```
The waveform in gtwave is shown below

![WhatsApp Image 2025-09-25 at 22 06 27](https://github.com/user-attachments/assets/c503ee07-1471-4e84-b20d-0c1f064c3964)


## Introduction to Yosys
It is the synthesizer used to convert RTL to netlist.
Netlist should be the same as the Design but represented in the form of standard cells.
The same testbench can be used to verify RTL and Synthesized Netlist.

<img width="1866" height="989" alt="Screenshot 2025-09-25 155905" src="https://github.com/user-attachments/assets/5c4e22bd-e9b2-411a-82f0-04d6f44dbf71" />


## Introduction to Logic Synthesis

<img width="1866" height="1079" alt="Screenshot 2025-09-25 160029" src="https://github.com/user-attachments/assets/c40f7df7-8755-46ca-8256-4d1be34bf77a" />
<img width="1427" height="1027" alt="Screenshot 2025-09-25 160201" src="https://github.com/user-attachments/assets/fca35285-04ca-48e9-8198-52b582494c04" />



## Lab using Yosys and Sky130 PDKs

![WhatsApp Image 2025-09-25 at 22 11 10](https://github.com/user-attachments/assets/ecc2c7d5-348e-41b5-957b-105ca47d984e)
![WhatsApp Image 2025-09-25 at 22 11 11](https://github.com/user-attachments/assets/43262f4d-e8d5-432b-8c6e-9b0270c633a9)
![WhatsApp Image 2025-09-25 at 22 11 11 (1)](https://github.com/user-attachments/assets/ffbc2664-5853-46d3-9737-338b67a4a5d8)
![WhatsApp Image 2025-09-25 at 22 11 11 (2)](https://github.com/user-attachments/assets/b4e68d4b-f8cc-4f32-8aff-07cd76372254)





</details>
<details>
<summary>Day 2 - Timing libs, Hierarchical vs Flat Synthesis and Efficient Flop Coding Styles </summary>
# Day 2 - Timing libs, Hierarchical vs Flat Synthesis and Efficient Flop Coding Styles
Standard cell libraries are characterized across **PVT (Process, Voltage, Temperature)** conditions to model real-world variations.  

- **Process (P):** Variations due to fabrication (e.g., fast, slow, typical).  
- **Voltage (V):** Variations in supply voltage levels.  
- **Temperature (T):** Variations in operating temperature.  

💡 Library filenames typically encode these conditions:  
- **`tt`** → Typical process corner  
- **`025C`** → Characterized at 25 °C  
- **`1v80`** → Characterized at 1.8 V  

**Example:**  
my_lib_tt_025C_1v80.lib
This corresponds to **typical process**, **25 °C temperature**, and **1.8 V voltage**.

<img width="827" height="834" alt="Screenshot 2025-09-25 212420" src="https://github.com/user-attachments/assets/323e2dfc-d72e-46eb-81b5-c88c10fb4109" />

## Hierarchical vs Flat Synthesis

### Hierarchical Synthesis
##  Report after Synthesizing `multiple_modules.v`

After synthesis, the **sub-module statistics** are printed.  
- Example: `sub_module1` has **1 AND gate** and `sub_module2` has **1 OR gate**.  
- This demonstrates **Hierarchical Synthesis**.

###  Hierarchy Preservation
- `sub_module1` and `sub_module2` are instantiated separately in the synthesized Verilog netlist.  
- Instead of directly showing AND/OR gates, we see the **sub-modules** when running the `show` command (as seen in the screenshot).  

###  Inside `sub_module2` (in synthesized netlist `multiple_modules_hier.v`)
- Instead of an OR gate, the inputs **a & b** pass through an **inverter** and then a **NAND gate**.  
- Reason: In CMOS, stacking **PMOS** (as in an OR gate) is inefficient, since PMOS has lower mobility and must be made wider to achieve meaningful output.  
- Hence, the synthesis tool optimizes the OR function into **INV + NAND**.  

👉 The next step is to analyze the **`.lib` file** for deeper understanding of how this mapping is performed.
![WhatsApp Image 2025-09-26 at 17 05 16](https://github.com/user-attachments/assets/3f26a54c-21e9-461f-ad18-d3e7dc70a8f6)
![WhatsApp Image 2025-09-26 at 17 05 59](https://github.com/user-attachments/assets/fa10a091-a057-4c58-88a3-b113eb0cb26f)
![WhatsApp Image 2025-09-26 at 17 05 15](https://github.com/user-attachments/assets/8573f77f-7c78-496b-96b6-89a20332a4a9)
### Flat Synthesis
The design can be flattened by using the command `flatten`.

Screenshot shows the command, synthesized netlist and the logical diagram.
<img width="1655" height="850" alt="Screenshot 2025-09-26 164629" src="https://github.com/user-attachments/assets/9dbec2cc-ce0d-43b1-ae39-0431a95bcd31" />
### Sub-module Level Synthesis
RTL (Register Transfer Level) designs are often modular, with various functional blocks or sub-modules. Sub-module level synthesis allows each of these sub-modules to be synthesized independently.
## 🔎 Why Sub-Module Level Synthesis?

Sub-module level synthesis is an important step in digital design because it allows finer control and better results during optimization. Some key benefits are:

- **Optimization and Area Reduction:**  
  Each sub-module is optimized on its own. The synthesis tool can apply logic optimizations, technology mapping, and area minimization locally, which leads to efficient use of resources and a smaller overall chip area.  

- **Reusability:**  
  Sub-modules can be designed, verified, and optimized independently. Once created, they can be reused across multiple designs, saving effort and improving design productivity.  

- **Parallel Processing:**  
  For large projects, different sub-modules can be synthesized in parallel. This makes the overall synthesis process faster and more scalable.  

---

### 🛠️ Running Sub-Module Synthesis  

Below are the commands to synthesize a specific sub-module:  

```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top sub_module1
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="830" height="616" alt="Screenshot 2025-09-26 171240" src="https://github.com/user-attachments/assets/179b8fe8-8956-4d03-a4f4-86783acaf06f" />











