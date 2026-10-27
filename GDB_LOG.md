# GDB log

One dated section per week. Paste the commands you used and the output you saw, then explain what it means in your own words. Pasted output with no explanation earns partial credit at most.

## Week 10 (Lab 5)

### Part A: basics with `buggy_rooms.cpp`

Build and run:

```
cmake -S . -B build
cmake --build build --target buggy_rooms
./build/buggy_rooms
```

Record:

- The backtrace (`bt`) after the crash:

#0  std::operator<< ... at .../basic_string.h:227

#1  0x00005555555563de in describe (room=0x0) at /home/ubuntu/lab10/gdb/buggy_rooms.cpp:36

#2  0x00005555555566b6 in main () at /home/ubuntu/lab10/gdb/buggy_rooms.cpp:53

- The function and line number where your own code crashed (use `frame N` to move to it):
 buggy_rooms.cpp:36

- The value of the pointer that caused the crash (`print`):

it printed out room = 0x0, which was the nullptr, which causes a segmentation fault when an attempt is made to read or stream its data.

- How you stepped through the program (`break main`, `run`, `next`, `step`): list the commands and what each showed:

Break main command, Sets the breakpoint for the entry point of the main function so execution pauses from the start.

run command is executing the program from the beginning until it hits a breakpoint or crashes the program.

next command. Executes the next line of code line by line without diving inside external function calls.

step command, steps directly in the function call on the current line to trace execution step by step inside sub functions.


- The fix, and how you checked it:

I added a safeguard null check like (room == nullptr) inside the describe() function to safely handle any missing room pointers

How i checked? I compiled by using cmake --build build --target buggy_rooms, Re-run the ./build/buggy_rooms and verified that it completed execution without segmentation faults.

### Part B: memory with `deck_bugs.cpp`

Build with AddressSanitizer and run each bug on its own (`./deck_bugs 1`, `2`, `3`).

For each bug, write one or two sentences: what kind of error AddressSanitizer reported, where the memory was allocated, and where it was freed.

| Bug | Report kind | Allocated at | Freed at | Cause in one sentence |
| --- | --- | --- | --- | --- |
| 1 | Memory Leak|deal_a_hand()|Never freed | Dynamic Memory was allocated with new but delete[] was nenver called before the pointer went out of the scope.   |
| 2 |Double Free or Invalid Free |Constructor | Freed twice |  A shallow copy caused two Deck objects to point to the exact same dynamically allocated card array which leads both to attempt deleting it upon destruction|
| 3 |Double Free |In code | Freed twice | The code explictly called delete or deleted the same pointer twice on the same memory address   |

Confirm one bug in GDB. Use at least two of `watch`, `finish`, and `x`. Paste the session and explain what each command showed:

`finish` command. Ran until the `deal_a_hand()` function returned back to `main`, confirming the function execution flow

`x/4xw &which`: Examined raw memory at the address of the `which` string variable, showing its underlying hex values.


Fixes for all three bugs (describe each change):
