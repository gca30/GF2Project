
# Grammar

## BNF Grammar

```
file = { filestmt }, EOF;
filestmt = connectlist | devicelist | defineblock;

devicelist = "device", NAME, devicedecl, { ",", devicedecl }, ";";
devicedecl = NAME, [ "{", NAME, "=", NUMBER, {",", NAME, "=", NUMBER}, "}" ];

defineblock = "define", NAME, ":", { definestmt }, "end", ";";
definestmt = connectlist | devicelist | inputlist | outputlist;

inputlist = "input", iodecl, { ",", iodecl }, ";";
outputlist = "output", iodecl, { ",", iodecl }, ";";
iodecl = NAME, [ "[", NUMBER,  "]" ];

connectlist = "con", condecl, { ",", condecl }, ";";
condecl = NAME, [ "[", index, "]" ], { ".", NAME, [ "[", index, "]" ] };
index = NUMBER | slice;
slice = [NUMBER], ":", [NUMBER], [":", [NUMBER]];

```

Devices with 0 inputs, 1 outputs: switch, clock, 0, 1


## Examples

```

# https://microcontrollerslab.com/cd4008-4-bit-full-adder-ic-pinout-features-example-datasheet/
define FULL_ADDER:
    input A, B, CarryIn;
    output S, CarryOut;

    device XOR xor1, xor2;
    device AND and1, and2;
    device OR or;

    con A, xor1.I1, and1.I1; 
    con B, xor1.I2, and1.I2;
    con CarryIn, xor2.I1, and2.I1;
    con xor1.O, xor2.I2, and2.I2;
    con and1.O, or.I1;
    con and2.O, or.I2;
    con xor2.O, S;
    con or.O, CarryOut;
end;


# https://www.engineersgarage.com/vhdl-tutorial-21-designing-an-8-bit-full-adder-circuit-using-vhdl/
define ADDER_8BIT:
    input A[8], B[8];
    output S[8], CarryOut;

    device FULL_ADDER adders[8];
    
    con adders.A, A;
    con adders.B, B;
    con adders.S, S;
    con adders[0].CarryIn, 0;
    con adders[:7].CarryOut, adders[2:].CarryIn;
    con adders[8].CarryOut, CarryOut;
end;

device SWITCH A[8] {value=0}, B[0] {value=0};
device ADDER_8BIT adder;
con A, adder.A;
con B, adder.B;
```


```
# https://en.wikipedia.org/wiki/Multiplexer#/media/File:4to1_MUX_using_basic_gates.jpg
define NOT:
    input I;
    output O;
    device NAND nand;
    con nand.I, nand.I1, nand.I2;
    con nand.O, O;
end;

define MUX2:
    input S0, S1;
    input I0, I1, I2, I3;
    output X;
    device NOT not0, not1;
    device AND and1, and2, and3, and4;
    device OR or1, or2, or3;
    con S0, not1.I, and2.I1, and4.I1;
    con S1, not0.I, and3.I1, and4.I2;
    con not1.O, and1.I1, and3.I2;
    con not0.O, and1.I2, and2.I2;
    con I0, and4.I3;
    con I1, and3.I3;
    con I2, and2.I3;
    con I3, and2.I3;
    con and1.O, or1.I1;
    con and2.O, or1.I2;
    con and3.O, or2.I1;
    con and4.O, or2.I2;
    con or1.O, or3.I1;
    con or2.O, or3.I2;
    con or3.O, X;
end;

# con input_port, {input_of_device}
# con outut_of_device, {input_of_another_device}
# con output_of_device, output_port


con 0, 1;

device SWITCH S0 {value=0}, S1 {value=0};
device SWITCH I0 {value=1}, I1 {value=1}, I2 {value=0}, I3 {value=0};
device MUX2 mux;
con mux.S0, S0; con mux.S1, S1;
con mux.I0, I0; con mux.I1, I1; con mux.I2, I2; con mux.I3, I3;
# do m mux.X to read the output, it should initialy be 1

```