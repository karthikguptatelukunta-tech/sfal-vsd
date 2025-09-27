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


## Combinational Logic Optimizations
Commands for optimization

```
opt_clean -purge
```
### Optimization of opt_check.v
Syntax for opt_check.v
```
module opt_check (input a , input b , output y);
        assign y = a?b:0;
endmodule
```
For opt_check.v the assignment `y = a?b:0` reduces to `y = ab`. The screenshot shown below explains this
<img width="533" height="304" alt="326625012-f4b6a999-f665-412f-a705-9496bfdd04c2" src="https://github.com/user-attachments/assets/8af34607-5afe-42fd-9aea-7175625f786d" /> 


The logic implementation after synthesis for opt_check.v is shown below, showing only AND gate.

![WhatsApp Image 2025-09-27 at 12 51 17](https://github.com/user-attachments/assets/dbb3d68d-cd8a-4c8d-bc96-44adf925213e) 

### Optimization of opt_check2.v
Syntax for opt_check2.v
```
module opt_check2 (input a , input b , output y);
        assign y = a?1:b;
endmodule
```
For opt_check2.v the assignment `y = a?1:b` reduces to `y = a + b`. 

The logic implementation after synthesis for opt_check2.v is shown below, showing only OR gate.

![WhatsApp Image 2025-09-27 at 12 52 40](https://github.com/user-attachments/assets/8aecb0a7-56db-4884-a67f-4cd505c39e04) 


### Optimization of opt_check3.v
Syntax for opt_check3.v
```
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```
For opt_check.v the assignment `y = a?(c?b:0):0` reduces to `y = abc`. The screenshot shown below explains this.

<img width="541" height="286" alt="326627801-c9fb59d8-d080-4776-bec6-46e3b48b3d68" src="https://github.com/user-attachments/assets/f1daa2f0-7264-43a4-96e6-77b40090152c" />

The logic implementation after synthesis for opt_check3.v is shown below, showing 3 input AND gate.

![WhatsApp Image 2025-09-27 at 12 54 44](https://github.com/user-attachments/assets/e511efd0-0239-4834-aa7e-eb66165ea837)


### Optimization of multiple_module_opt.v

Syntax of multiple_module_opt.v
```
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule

module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule

module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 
endmodule
```

The logic implementation after synthesis for multiple_module_opt.v is shown below.

![WhatsApp Image 2025-09-27 at 12 55 58](https://github.com/user-attachments/assets/72816d7c-32b8-45cd-affa-10ba122b57af)


## Sequential Logic Optimizations

Both the dff_const1.v and dff_const2 are explained below.

<img width="851" height="415" alt="326765445-b9bede59-edaa-4f4f-ad90-9036c63aa4da" src="https://github.com/user-attachments/assets/fcffee66-49e3-40a4-b13a-39f575bc804e" />
### Optimizing dff_const1.v

Syntax for dff_const1.v
```
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end

endmodule
```
For dff_const1.v, `q=0` as long as `reset=1`. However, when `reset=0` `q` doesn't immediately becomes `1` rather at the next rising edge of the `clk` as shown below. ***So the optimization cannot be applied***.

![WhatsApp Image 2025-09-27 at 13 18 30](https://github.com/user-attachments/assets/0788ed96-75ad-4c3c-92cb-5d1bb781b7a6)

The commands to run the synthesis
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

The logic implementation after synthesis for dff_const1.v is shown below.
![WhatsApp Image 2025-09-27 at 13 20 31](https://github.com/user-attachments/assets/f177515a-b1c0-4b9a-8dea-e32c7d7a749f)
similarly,
![WhatsApp Image 2025-09-27 at 13 22 24](https://github.com/user-attachments/assets/de7fa636-4688-46ac-89dc-841479ea562e)
![WhatsApp Image 2025-09-27 at 13 22 24 (1)](https://github.com/user-attachments/assets/274aa7c9-fd36-4591-9eba-4560c91bc4d0)
![WhatsApp Image 2025-09-27 at 13 22 25](https://github.com/user-attachments/assets/13b9be48-0275-4f29-986b-c8319bbd752a)
![WhatsApp Image 2025-09-27 at 13 22 25](https://github.com/user-attachments/assets/ecdb25bd-3c76-4182-9d8c-f61836a52b1b)

### Optimizing dff_const3.v (above images)

Syntax for dff_const3.v
```
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```
For dff_const3.v, there are two flops.  `q1=0` as long as `reset=1`. However, when `reset=0` `q1` doesn't immediately becomes `1` rather at the next rising edge of the `clk` with some propagation delay as shown below. `q=1` as long as `reset=1`, acting as `set` rather than `reset`. However, when `reset=0` `q` samples `q1` as `0` as there are some propagation delay for `q1`as shown below. At the next `clk` edge `q` samples `q1` as `1`.
***So the optimization cannot be applied***.

The command to run HDL simulation
```
iverilog dff_const3.v tb_dff_const3.v
./a.out
gtkwave tb_dff_const3.vcd
```
The HDL simulation is shown below.

The commands to run the synthesis
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

## Sequential Optimzations for Unused Outputs

### Optimization of Case1: 3-bit Up Counter with q[0] used (counter_opt.v)
Example of a counter where bits at the position of [2] and [1] are unused.

```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
The screenshot explains the logic of the counter. Only q[0] is used. ***So the optimization can be applied***.

<img width="1200" height="506" alt="326786452-dbdbd8d5-a305-4f31-b8ba-a8c33d53d67a" src="https://github.com/user-attachments/assets/a7d3bedd-a022-4f98-a7b4-2cbe1e0ce3c1" />
The commands to run the synthesis
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
We see only one flop after the synthesis and also seen in synthesis report after `synth -top counter_opt.v`

<img width="1672" height="506" alt="326789951-42d260a1-72bc-4d5f-b891-9fea58a57813" src="https://github.com/user-attachments/assets/56b9a09b-29eb-4344-80d1-abd4c037d5d8" />
### Optimization of Case2: 3-bit Up Counter (counter_opt2.v)

Example of a counter where all the bits are used.
```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count[2:0] == 3'b100);

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
The commands to run the synthesis
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="829" height="672" alt="326793539-a7c68bda-5619-4dd6-89d4-d8773c1bf345" src="https://github.com/user-attachments/assets/4d88e2ab-6c80-4d9f-afe0-4e5122fc8567" />
<img width="1669" height="386" alt="326793572-95bbe05c-bdde-4bdd-8fe7-492be015dc9f" src="https://github.com/user-attachments/assets/a3d29d01-00ed-4601-a80a-8c23604e6e0b" />
<img width="1167" height="471" alt="326793600-11cd582b-4ccd-4b99-82e2-1c68c92db131" src="https://github.com/user-attachments/assets/9f159fe7-ca84-4348-b26d-2a2081b2dc7c" />

</details>
<details>

 <summary>Day 4 - GLS, Blocking vs Non-blocking and Synthesis-Simulation Mismatch</summary>
 
# Day 4 - GLS, Blocking vs Non-blocking and Synthesis-Simulation Mismatch

## GLS, Synthesis-Simulation Mismatch, and Blocking/Non-blocking Statements
After synthesis, it’s important to validate the design to make sure it still behaves as expected. The key checks are:

-**Functional Verification**
Run simulations on the synthesized netlist to confirm that functionality matches the RTL design.

-**Timing Verification**
Ensure the design meets timing requirements. This is typically done through delay annotation (timing-aware simulation), where real gate delays from the .lib are applied to the netlist.
<img width="529" height="240" alt="Screenshot 2025-09-27 133734" src="https://github.com/user-attachments/assets/27b17251-51ca-4089-938e-131cb0c4aefd" />

### Synthesis Simulation Mismatches

It happens because of the following reasons
- Missing sensitivity list
- Blocking vs non-blocking assignments
- Non-standard verilog coding

#### (1) Missing sensitivity list

As shown in the screenshot below, `always` block is evaluated only when `sel` is changing. So output `y` is not evaluated when `sel` is not changing although `i0` and `i1` are changing. Rather it acts like a latch. The code on the right side represents the correct design coding for `mux`. In this case `always` is evaluated for any signal changes. 

<img width="525" height="304" alt="Screenshot 2025-09-27 133744" src="https://github.com/user-attachments/assets/7ed95325-ac5a-4bc2-b595-23082fd3650a" />

#### (2) Blocking vs Non-blocking Assignments
##### Blocking Statements
-Denoted by =
-Executed sequentially in the order they appear inside the always block
-Each statement completes before the next one starts
##### Non-Blocking Statements
-Denoted by <=
-All right-hand side (RHS) expressions are evaluated when the always block is entered, and updates to the left-hand side (LHS) happen in parallel
-Enables concurrent execution


 The left side of the screenshot below gives us the correct execution. While the right side can lead to serious issues as `d` is assigned to `q` directly. ***So choosing non-blocking statements is best practice*** (highlighted in the screenshot below).
 <img width="509" height="269" alt="Screenshot 2025-09-27 133754" src="https://github.com/user-attachments/assets/20227375-233f-4d74-9530-cf4e0a2e1c1d" />

##### Blocking Statements Leading to Synthesis Simulation Mismatch

In the code shown below, `y` gets the old `q0` value. This will mimic delay or flop. But when you synthesize, there will be no flop. If the order is changed (right side code), latest value of `q0` is assigned to `y`. 

When synthesized, both will lead to the same circuit. However, simulation will result in different behavior. For the left side of the code, `y` gets the old `q0` value and for the right side of the code, `y` gets the latest `q0` value leading to a synthesis simulation mismatch. 

This issue is resolved by using ***non-blocking statements***.

<img width="499" height="265" alt="Screenshot 2025-09-27 133803" src="https://github.com/user-attachments/assets/36eeb1e4-a567-4d34-b686-3b1908e2ebf7" />


## Labs on GLS and Synthesis-Simulation Mismatch

### Ternary operator MUX (ternary_operator_mux.v)

The Verilog code of ternary_operator_mux.v
```
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
```
The command to run HDL simulation
```
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
HDL Simulation waveform of ternary_operator_mux.v is shown in the screenshot below

![WhatsApp Image 2025-09-27 at 14 44 31](https://github.com/user-attachments/assets/1adbe962-051e-4015-873e-876cf9a1206c)
The commands to run the synthesis for ternary_operator_mux.v
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog ternary_operator_mux_net.v
```
![WhatsApp Image 2025-09-27 at 14 46 21](https://github.com/user-attachments/assets/865e444d-cbf9-4b06-9deb-be98db7d60ea)
The commands to do GLS for ternary_operator_mux.v
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
The GLS output is shown below.
![WhatsApp Image 2025-09-27 at 14 46 21](https://github.com/user-attachments/assets/3215cc74-b084-4a0a-a909-29200b9c086f)

### Bad MUX (bad_mux.v)

The `always` block is executed only at `sel` signal. It works like a flop rather than mux.
The Verilog code of bad_mux.v
```
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```

The command to run HDL simulation
```
iverilog bad_mux.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
HDL Simulation waveform of bad_mux.v is shown in the screenshot below
![WhatsApp Image 2025-09-27 at 14 49 25](https://github.com/user-attachments/assets/c3d9435a-9dcc-4bee-9856-e9cdedce5893)

The commands to run the synthesis for bad_mux.v.
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_mux.v
synth -top bad_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog bad_mux_net.v
```

The synthesis report shows it is still inferring the mux but not the flop.
![WhatsApp Image 2025-09-27 at 14 50 23](https://github.com/user-attachments/assets/5282f785-4846-4adf-b606-9dc42fb0beae)

The commands to do GLS for bad_mux.v
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
The GLS output is shown below. This shows correct functionality which is different from HDL simulation, leading to ***synthesis simulation mismatch***.

![WhatsApp Image 2025-09-27 at 14 51 35](https://github.com/user-attachments/assets/ae7b0fae-7986-4848-b347-44560acba3ca)

## Labs on Synthesis-Simulation Mismatch for Blocking Statements

### Blocking Caveat (blocking_caveat.v)

The logic to simulate is shown below.
<img width="594" height="346" alt="326985349-d7e6ea20-93fb-41d4-9f56-9910fe8f9931" src="https://github.com/user-attachments/assets/eb1049eb-884d-4dbe-aedd-fd4a5b89ef2c" />
The Verilog code of blocking_caveat.v
```
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```

The command to run HDL simulation
```
iverilog blocking_caveat.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
HDL Simulation waveform of blocking_caveat.v is shown in the screenshot below. `d` takes the old value of `x` causing incorrect functionality.
![WhatsApp Image 2025-09-27 at 14 54 42](https://github.com/user-attachments/assets/93abda7b-be6a-4eee-9885-edf715237edd)

The commands to run the synthesis for bad_mux.v.
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog blocking_caveat.v
synth -top blocking_caveat
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog blocking_caveat_net.v
```

The synthesis report and logic synthesis is shown below.
![WhatsApp Image 2025-09-27 at 14 56 04](https://github.com/user-attachments/assets/4c8b9c8b-d978-4327-94c6-cea1ec14c8bb)
The commands to do GLS for bad_mux.v
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_net.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```

The GLS output is shown below. In this case, `d` takes the current value of `x` causing incorrect functionality.The waveform shows correct functionality which is different from HDL simulation, leading to ***synthesis simulation mismatch***.
<img width="981" height="463" alt="326988314-b8fcd356-8743-4098-aae8-bdbd8ab88a15" src="https://github.com/user-attachments/assets/6e589a90-5d92-4f83-a269-85337e00b0be" />
</details>

<details>

 <summary> Day 5 - Optimization in Synthesis </summary>
**# Day 5 - Optimization in Synthesis**

## If Case Constructs
The `if-else` statement in Verilog is mainly used to create **priority logic**.  
It evaluates conditions in sequence, giving higher priority to the first matching condition.  

- If the first condition is true, all other conditions are ignored.  
- Only when the first condition is false, the second is checked, and so on.  
- The final `else` acts as a default when none of the conditions are true.  

---

## Example of Priority Logic

```verilog
if (cond1) begin
    // Code for condition 1
end else if (cond2) begin
    // Code for condition 2
end else if (cond3) begin
    // Code for condition 3
end else begin
    // Default code
end
```
One common mistake with if-else coding is creating inferred latches due to incomplete condition
<img width="1373" height="709" alt="Screenshot 2025-09-27 162607" src="https://github.com/user-attachments/assets/74db36c1-a2b8-4cf8-b783-91f121893184" />
<img width="1364" height="776" alt="Screenshot 2025-09-27 162803" src="https://github.com/user-attachments/assets/36ac072e-587a-456e-b3e5-fb988cc47d73" /> 

## Labs on Incomplete If case,Incomplete overlapping case
Some of executed screenshots
<img width="1819" height="863" alt="Screenshot 2025-09-27 182511" src="https://github.com/user-attachments/assets/ea179fb7-c7a9-4b40-a8b1-c089b5f4b793" />
<img width="1565" height="785" alt="Screenshot 2025-09-27 182611" src="https://github.com/user-attachments/assets/22b382fb-d13d-416b-a62b-b226b3fb7348" />
<img width="1828" height="623" alt="Screenshot 2025-09-27 182724" src="https://github.com/user-attachments/assets/c6c2792b-0daf-488e-8e6d-cb21b322e741" />
<img width="1851" height="506" alt="Screenshot 2025-09-27 182751" src="https://github.com/user-attachments/assets/bd6edf26-51ce-4c29-ae15-3dba199c1001" />
## for loop,for generate

#### `for` Loop
- Used inside **procedural blocks** (`always`, `initial`).
- Executes **sequentially** in simulation.
- Mainly for **testbenches** or **simulation code**, but can also be used for RTL when unrolled by synthesis tools.
- Example:
  ```
  module for_example;
    reg [3:0] arr [0:3];
    integer i;
    
    initial begin
      for (i = 0; i < 4; i = i + 1) begin
        arr[i] = i;   // sequentially assigns values
      end
    end
  endmodule
  ```
### generate Loop
-Used for structural replication of hardware at elaboration (compile) time.

-Executes in parallel – creates multiple instances of hardware.

-Must be declared inside a generate ... endgenerate block.

-Useful for instantiating repeated modules, wires, or registers.

Example:
```
module dff(input clk, d, output reg q);
  always @(posedge clk) q <= d;
endmodule

module generate_example(input clk, input [3:0] d, output [3:0] q);
  genvar i;
  generate
    for (i = 0; i < 4; i = i + 1) begin : dff_loop
      dff u_dff (.clk(clk), .d(d[i]), .q(q[i]));
    end
  endgenerate
endmodule
```
<img width="937" height="523" alt="Screenshot 2025-09-27 183017" src="https://github.com/user-attachments/assets/11ca269c-848f-4604-b067-9b8875d07ac2" />
<img width="930" height="525" alt="Screenshot 2025-09-27 183043" src="https://github.com/user-attachments/assets/8ae5d529-cbae-491e-8e77-7c0cc7aaf5b1" />
<img width="937" height="523" alt="Screenshot 2025-09-27 183059" src="https://github.com/user-attachments/assets/cbbf05c9-cfc3-496e-924b-b1c49df2d74b" />

##Labs on for loop and for generate
<img width="1203" height="319" alt="Screenshot 2025-09-27 183535" src="https://github.com/user-attachments/assets/f77beb8f-72db-4aec-bbc9-9c5c255edc06" />

<img width="1832" height="590" alt="Screenshot 2025-09-27 183454" src="https://github.com/user-attachments/assets/a73a9880-386d-4c54-ade2-59c38c21c7da" /> 
</details>


































