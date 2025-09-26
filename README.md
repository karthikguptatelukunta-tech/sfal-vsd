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


## Various Flop Coding Styles and Optimization

### Why do we need flops and how do they prevent glitches in the circuit?

Why Flops Help Prevent Glitches

Glitches in digital circuits often show up because of signal delays, noise, or timing mismatches. Flip-flops (flops) play a big role in keeping things clean and stable:

Synchronization: Flops are edge-triggered. They only “listen” on clock edges (rising or falling), so outputs don’t randomly toggle when inputs wiggle in between. This makes sure the output updates in a controlled way, not due to short-lived glitches.

Timing Control: Since flops are driven by a clock, all updates happen in sync across the design. That clocked behavior prevents mismatched arrival times of signals from creating unwanted glitches.

In short, flops act like a timing checkpoint—capturing signals only at defined moments and filtering out noise or spurious transitions.
<img width="846" height="867" alt="324639488-32aab966-261a-4e42-9f49-59572586cd0f" src="https://github.com/user-attachments/assets/158f932d-3750-4b2e-b4eb-b9c14284dfa4" />
### Different types of flops
To initialize flops, we need to `set` and `reset` which can be synchronous or asynchronous
<img width="775" height="423" alt="324651651-338b941f-4a51-4cf3-9289-f344afac2922" src="https://github.com/user-attachments/assets/d632e03d-d85e-4c3f-a153-1859446967c4" />
![WhatsApp Image 2025-09-26 at 23 17 53](https://github.com/user-attachments/assets/2fa482f7-80d5-4e0b-9de8-b41db0cf03bd)
![WhatsApp Image 2025-09-26 at 23 17 54](https://github.com/user-attachments/assets/8cfddd98-f0f4-4c90-9122-a101d112d388)
![WhatsApp Image 2025-09-26 at 23 17 54 (1)](https://github.com/user-attachments/assets/de4d41ed-ac60-4485-b50c-1b3d5d87df60)
![WhatsApp Image 2025-09-26 at 23 17 55](https://github.com/user-attachments/assets/670a5892-c34a-46fc-a697-a3d9cb0e7285)
![WhatsApp Image 2025-09-26 at 23 17 55 (1)](https://github.com/user-attachments/assets/653bd990-7c62-46ac-b364-f9b04fd70cbf)
![WhatsApp Image 2025-09-26 at 23 17 55 (2)](https://github.com/user-attachments/assets/6c9c12ff-0849-4fd0-a8b9-29995f926b59
![WhatsApp Image 2025-09-26 at 23 17 56](https://github.com/user-attachments/assets/adaedfb0-d5cc-4368-a9a8-f646cb889e4d)

### Synthesizing flops
The command to synthesize ***DFF with asynchronous reset*** as an example
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![WhatsApp Image 2025-09-26 at 23 17 55](https://github.com/user-attachments/assets/b3cd999e-5789-4f96-9a9d-ba05d3e3510a)
![WhatsApp Image 2025-09-26 at 23 17 55 (1)](https://github.com/user-attachments/assets/c51ad20c-ea30-4a2c-aff5-eaab1eb4a033)
![WhatsApp Image 2025-09-26 at 23 17 55 (2)](https://github.com/user-attachments/assets/e95f6003-3de8-4ee5-bc64-6c43f1ef0568)
![WhatsApp Image 2025-09-26 at 23 17 56](https://github.com/user-attachments/assets/dbc90c93-e363-4539-8c6d-fbcdb1a77a90)

### Synthesizing mult2 (multiply by 2)

 
To implement `y[3:0] = 2*a[2:0]`, we append a `1'b0 `to the `a[2:0]` i.e, `y[3:0] = {a[2:0],0}`. This is also equal to left shift the input bits by 1.
This can be realized by just wiring.
So we expect no hardware which is also seen in the screenshot below, analysis after synthesis and show. The command 'abc' is not required for mapping when there are no cells.

![WhatsApp Image 2025-09-26 at 23 29 03](https://github.com/user-attachments/assets/178dc861-bfbd-4b97-8324-2185ce4d2ed4)
![WhatsApp Image 2025-09-26 at 23 29 04](https://github.com/user-attachments/assets/9c8b030d-bb94-4187-b01f-436ce7d4896b)
![WhatsApp Image 2025-09-26 at 23 29 04 (1)](https://github.com/user-attachments/assets/39744db2-3532-482c-b055-7aa5de7f1070)
### Synthesizing mult9 (multiply by 9 or 8+1)

`y=9*a` can be considered `8*a+1*a`
To implement `y[5:0] = 9*a[2:0]`, we append `000` to `a[2:0]` and then add `a` i.e, `y[5:0] = {a[2:0],000} + a[2:0]`.
This can be realized just by wiring.
So we expect no hardware which is also seen in the screenshot below, analysis after synthesis and show. The command 'abc' is not required for mapping when there are no cells.
![WhatsApp Image 2025-09-26 at 23 30 52](https://github.com/user-attachments/assets/86cab940-27b5-44b8-b07c-b7f3cf8c8d53)
![WhatsApp Image 2025-09-26 at 23 31 29](https://github.com/user-attachments/assets/80acf8a2-61de-433c-82f5-73e8d5e1112b)
![WhatsApp Image 2025-09-26 at 23 30 52 (1)](https://github.com/user-attachments/assets/2330f8ca-a128-4d59-ae5d-c539b430c210
</details>

<details>
	<summary>Day 3 - Combinational and Sequential Optimizations </summary>
	
# Day 3 - Combinational and Sequential Optimizations

## Introduction to Optimizations

### Combinational Logic Optimization
Logic optimization is all about squeezing the design to make it more efficient in terms of area and power.

Two of the most common techniques are:

Constant Propagation – simplifying logic by directly replacing signals with known constant values.

Boolean Logic Optimization – using methods like K-maps or Quine-McCluskey to minimize expressions.
<img width="618" height="324" alt="326310525-c8bd1118-52f7-441b-8cff-254d851cb892" src="https://github.com/user-attachments/assets/c5babb75-241e-4a5c-9f56-66b47b66b19c" />


one such example is shown below
<img width="619" height="300" alt="326311271-aa864102-ef33-4d45-9ec9-929738172cd4" src="https://github.com/user-attachments/assets/62166bce-2939-4c58-999f-dc6d233f04f3" />
### Sequential Logic Optimization
The technqiues used are:
1) Basic
   - Sequential constant propagation
2) Advanced (not covered as part of lab)
   - Static optimization
   - Retiming
   - Sequential logic cloning (floorplan aware synthesis)

An example of sequential constant propagation is highlighted below of DFF with asynchronous reset where D input is grounded. To note, the same technique cannot be applied to DFF with the asynchronous set because while `Q=1` when `Set=1`, but `Q=0` at `Set=0` at the next CLK pulse. Q is dependent not only on Set but also on the clock edge.
<img width="629" height="348" alt="326312711-3e31a212-a0b0-42c3-be92-d0075a9f7d1c" src="https://github.com/user-attachments/assets/8d26b664-c3cc-424e-81b5-6f34c832083c" />
Retiming is a technique to improve the performance of the circuit.
<img width="600" height="337" alt="326621258-23bcc15c-813b-496a-aebf-ebbf5ceba557" src="https://github.com/user-attachments/assets/538164d5-418a-489b-94f6-83d27305d7ad" />
























