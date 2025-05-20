
# Grammar

## BNF Grammar

```
file = { statement }, eof;

statement = connectlist | inputlist | outputlist | devicelist | defineblock;

inputlist = "input", NAME, { ",", NAME }, ";";
outputlist = "output", NAME, { ",", NAME }, ";";
devicelist = "device", NAME, NAME, { ",", NAME }, ";";
defineblock = "define", NAME, ":", { statement }, "end", ";";

connectlist = "con", io, { ",", io }, ";";
io = NAME, [ "[", index, "]" ], { ".", NAME, [ "[", index, "]" ] };
index = NUMBER | slice;
slice = [NUMBER], ":", [NUMBER], [":", [NUMBER]];

```

## Examples

```

define WEIRD_OP4:
    input A, B, C, D;
    output Out;
    device XOR myxor;
    device AND myand1, myand2;
    con A, myxor.I1;
    con B, myxor.I2;
    con myxor.O, myand2.I1;
    con myand1.O, myand2.I2;
    con C, myand1.I1;
    con D, myand1.I2;
    con myand2.O, Out;
end;

input inputbus[400], outputbus[100];
device WEIRD_OP4 my_weirdop[100];
con my_weirdop[:].A, inputbus[1::4];
con my_weirdop[:].B, inputbus[2::4];
con my_weirdop[:].C, inputbus[3::4];
con my_weirdop[:].D, inputbus[4::4];
con my_weirdop[:].Out, outputbus[:];
my_weirdop[]

output X;
con X, my_weirdop.A;


```