IGB Compiler L1 compiles **IGB L1 code** into **IGB Binary**.  
You may call it an *assembly language*.  


The [IGB CL2](https://github.com/krypciak/IGB-Compiler-L2) compiles high-level code into this low-level code.  

The instruction set file can be seen [here](/resources/commandSet.txt).
## Commant set symbol explanation:
- `*string*|*number*` If the current argument equals *string*, *number* is returned.
- `i` The input value is returned as it is.
- `d` The input value gets multiplied by 1000
- `@` accepts two arguments:  
  - `n` that returns 0  
  - `c` that returns 1  
If it's next argument is 'd' and the current argument equals 'c', the next argument isn't multiplied by 1000.
- `P` If the argument is a string, returns the cell of a pointer. If the argument is an integer, returns it.

#### Example:  
 
Hint: |12| means cell 12


input: `Math % 10 c 11 12`  
syntax match: `Math|7 %|3 i @ d i`  
output: `7 1 10 1 11 12`
 
That instruction can be translated into `|12| = |10| % |12|`
<br /><br />
##### If you swap the 'c' for a 'n', the second last argument will be 1000 times bigger.
input: `Math % 10 n 11 12`  
syntax match: `Math|7 %|3 i @ d i`  
output: `7 1 10 0 11000 12`
 
That instruction can be translated into `|12| = |10| % 12`
<br /><br />

## Memory cells  
There are cells allocated for the [IGB VM](https://github.com/krypciak/IGB-VM).
You are safe to use cells 70 or above.
Here are some more important cells:
0. Function return
1. Screen width
2. Screen height
3. Screen type (`0=16c`, `1=rgb`) (more explained later)
4. Keyboard input char
56. [charLib](https://github.com/krypciak/IGB-charLib) char code
57. [charLib](https://github.com/krypciak/IGB-charLib) char x
58. [charLib](https://github.com/krypciak/IGB-charLib) char y  

The full list can be seen [here](/src/me/krypek/igb/cl1/IGB_MA.java).
<br /><br />

## Instruction explanation:

### 0. If
&emsp; Syntax:  `If *operation* i @ d P`  
&emsp; If cell `arg2` *operation* `arg4 / |arg4|` is false then jump to `arg5` (pointer or integer)  
<br />
&emsp; Example: `If > 70 n 5 :myfunction`  
&emsp; Which means: `If cell 70 is NOT higher than the number 5000 jump to :mypointer`

### 1. Init
&emsp; Syntax: `Init d i`  
&emsp; Writes `arg1` to cell `arg2`  
<br />
&emsp; Example: `Init 5.3 70`  
&emsp; Which means: `Write 5300 to cell 70`

### 2. Copy
&emsp; Syntax: `Copy i i`  
&emsp; Copies cell `arg1` to cell `arg2`  
<br />
&emsp; Example: `Copy 70 71`  
&emsp; Which means: `Read cell 70 and write it to cell 71`

### 3. Add
&emsp; Syntax: `Add i @ d i`  
&emsp; Adds cell `arg1` to `arg3 / |arg3|` and store the result in cell `arg4`  
&emsp; Why a seperate instruction for adding numbers? It's faster that way.  
<br />
&emsp; Example: `Add 70 c 71 72`  
&emsp; Which means: `Add cell 70 and cell 71 and store the result in cell 72`  

### 4. Cell
&emsp; Jump Syntax: `Cell Jump P`  
&emsp; Sets the current cell to `arg2`  
<br />
&emsp; Call Syntax: `Cell Call P`  
&emsp; Stores the current cell on a stack and sets the current cell to `arg2`  
<br />
&emsp; Return Syntax: `Cell Return`  
&emsp; Pops the stack and sets the current cell to it

### 5. Pixel
The screen type (`cell 3`) determinates which syntax will do what at runtime.  
<br />
RGB synax:
- `Pixel Cache @ i @ i @ i`
Sets the pixel cache to the arguments.  
If all arguments are `d`, the cache is computed at compile-time.  
Read more about pixel cache [here](https://github.com/krypciak/IGB-VM)
