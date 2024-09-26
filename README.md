# basys3
Digilent Basys 3 Projects

## Programming

The Vivado 2023.1 IDE is used to program the Basys3 board.
In the Vivado Project Wizard, the FPGA chip can be selected but there is also an option to select an entire board.

Here, the Basys3 board is selected.

The wizard will display the following values, when the Basys3 Board is selected:

Default Board: Basys3
Default Part: xc7a35tcpg236-1
Family: Artix-7
Package: cpg236
Speed Grade: -1

### Constraints file for the Basys3

https://digilent.com/reference/programmable-logic/guides/getting-started-with-vivado
https://www.physics.umd.edu/hep/drew/495/blinking.html

Even though the project has been created for the Basys3 board, there is no constraints
file added to the project. A constraints file is used to define symbols for IO-Pins.
Using these symbols in the HDL source code will automatically use the physical IO-Pins
to drive or read the signals defined in the HDL code. This means that by conventions,
using symbols in the HDL makes the compiler look up those symbols in the constraints
file and create a bitstream that triggers the correct physical pins.

Go to this page: https://github.com/Digilent/digilent-xdc
Download the file: https://github.com/Digilent/digilent-xdc/blob/master/Basys-3-Master.xdc
You can place the constraint file anyhwere since there is an option that makes Vivado copy
the file into the correct folder inside the project automatically.

In the Project Manager in Vivado > Constraints > constrs_1 > Add Sources > Add or create constraints
> Select the Basys-3-Master.xdc file > Select the checkbox: "Copy constraints file into project " > Finish

The file will be copied here: C:/aaa_se/fpga/basys3/SevenSegmentOutput/SevenSegmentOutput.srcs/constrs_1/imports/Downloads/Basys-3-Master.xdc

When you double click the file Basys-3-Master.xdc  inside vivado, an editor will open and display the
constraints file. You can see that all symbols are commented out. You need to uncomment symbols so you
can use them in the HDL files.

Add a new source file:
In the Project Manager > Design Sources > Context Menu: Add Sources > "Add or create design sources" > Next > Create File.
Call the file top.v

```
module top(
    input clk,
    output led
    );
    
    reg [24:0] count = 0;
 
    assign led = count[24];
 
    always @ (posedge(clk)) 
    begin
        count <= count + 1;
    end

endmodule
```

### Errors

```
ERROR: [DRC NSTD-1] Unspecified I/O Standard: 1 out of 2 logical ports use I/O standard (IOSTANDARD) value 'DEFAULT', 
instead of a user assigned specific value. This may cause I/O contention or incompatibility with the board power or 
connectivity affecting performance, signal integrity or in extreme cases cause damage to the device or the components 
to which it is connected. To correct this violation, specify all I/O standards. This design will fail to generate a 
bitstream unless all logical ports have a user specified I/O standard value defined. To allow bitstream creation with 
unspecified I/O standard values (not recommended), use this command: set_property SEVERITY {Warning} [get_drc_checks NSTD-1].  

NOTE: When using the Vivado Runs infrastructure (e.g. launch_runs Tcl command), add this command to a .tcl file and 
add that file as a pre-hook for write_bitstream step for the implementation run. Problem ports: led.
```

Check the very end of the error output. It says: "Problem ports: led."
No you know that the led port is the problem.

The solution to the error is that the constraints file has to be changed.
Do not uncomment the led[0] row but instead add a new row that looks like this:

```
set_property -dict { PACKAGE_PIN U16   IOSTANDARD LVCMOS33 } [get_ports {led}]
```

Now the led variable is defined propertly and will survive the bitstream generation.

### Further steps

Start the Synthesis.
Start the Implementation.
Generate Bitstream.

### Connect to the hardware



