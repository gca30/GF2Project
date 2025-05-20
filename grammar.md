
# Grammar

## BNF Grammar

```
file = { filestatement }, EOF;

filestatement = connectlist | devicelist | defineblock;

devicelist = "device", NAME, devicedecl, { ",", devicedecl }, ";";
devicedecl = NAME, [ "{", NAME, "=", NUMBER, {",", NAME, "=", NUMBER}, "}" ];

defineblock = "define", NAME, ":", { definestatment }, "end", ";";
definestatement = connectlist | devicelist | inputlist | outputlist;

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

```