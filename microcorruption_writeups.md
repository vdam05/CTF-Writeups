The website is microcorruption.com

For each problem, we are solving how to unlock the door through our password input. These cover reverse engineering and binary exploitation.
We can put hex encodings rather than characters if needed.

The architecture is similar to basic assembly, with a bit different AT&T syntax and gdb commands.

# How to Solve:

## Password

- Tutorial: It just checks for password length. Makes password length = 8 and the character at the end = 00

- New Orleans: Password is in set_password. 

- Sydney: Each input byte is compared in check_password. Put each byte in little endian form due to the cmp instructions.

## Buffer Overflows
- Hanoi: The entire function doesn't do anything we should care about. The cmp statement near "Access Granted" means we buffer overflow and override the value at a certain address with an immediate

- Cusco: Took a long time but it is a buffer overflow continuation. We override the old return address that is after the buffer
(after it printed "This password is not correct.") to point exactly to our unlock_door function.

## Hiding code in memory
- Reykjavik: It hides code so the code for inputting is hidden somewhere in memory as bytes. But best way is to continue until we hit where it asks for input. Then step through instructions from there. There is only 1 cmp statement hidden in code but not hidden in current instruction and program state screens.

## ROP
- Whitehorse: This program seems to be a buffer overflow as well. At the end of login(), we want to attack the ret instruction with a new address. 
    - The stack pointer will be pointing to somewhere in our buffer. But if change it to point to the right address, there is a hole in that the sp becomes messy and at the end of login() again, it will ret into some bad address and terminates. Therefore, there is another place for a return address to be put in our buffer, making it 2 ret addr in 1 string.
    - However, I suspect in here we have to make our own code for opening the door. Look at the previous levels for unlock_door(). All of it has a specific push and then a call to INT. Looks more like a ROP attack!!! If we look at INT, it reads 2 bytes up ahead for our value. Account for that in our string!

- Montevideo: No more code pointing to unlock_door. We have to do that ourselves. If we look at past challenges, we would see that they all invoke the same 2 lines where it pushes some value to the stack and call INT.

- There is 2 new method: strcpy and memset. New address of INT at 0x454c (differs each time). Therefore, find another way to do the same thing!
    - So we can get the byte codes and put in the buffer and then run it via a return address overwritten. However, because it uses strcpy, it copies until a null byte, leading to rest of input missing 
    - Therefore, we can reuse the result from Whitehorse, except we replace the padding bytes of 0s for the INT to take from sp, with something other than null bytes. So that our string won't end prematurely.

- Johannesburg: It builds on Montevideo, except it has a check to exit immediately if our password is too long. But the check depends on memory near buffer so we can use the right value to bypass that check! Requires 17 BYTES (value differs) and then the value it wants to check to be equal.
    - The value it checks is also just a byte and we won't need to fill in the next bytes since the buffer was an odd value. After that, it adds some bytes to the sp and we get to point our return address somewhere else again, just like Montevideo (still uses strcpy so needs the padding to be non-null)