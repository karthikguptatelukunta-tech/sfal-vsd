# sfal-vsd
## Day 0 - Tools Installation

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


![Yosys](https://github.com/user-attachments/assets/5e6a0386-e42b-4bac-8770-c5b333d23dc9)

### iverilog
```bash
$ sudo apt-get install iverilog

![iverilog](https://github.com/user-attachments/assets/1e5f143e-8087-48c4-bddf-82255f542e38)

###Gtkwave
```bash
$ sudo apt update
$ sudo apt install gtkwave

![gtkwave](https://github.com/user-attachments/assets/4ca9c5a9-bcb4-48ee-8bf6-e7cce3b33286)

![gtkwave1](https://github.com/user-attachments/assets/8cdc0abe-5b9e-4347-b22e-433751a126f1)
