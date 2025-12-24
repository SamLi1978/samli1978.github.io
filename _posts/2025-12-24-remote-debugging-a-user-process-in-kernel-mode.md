---
layout: post
title: Remote Debugging a User Process in Kernel Mode
date: 2025-12-24 11:08 -0500
categories: [Debugging]
tags: [WinDbg]
---

# Remote Debugging a User Process in Kernel Mode

Sometimes we want to trace from user mode into kernel mode to understand how parameters passed from user mode are implemented internally within Windows. However, a significant challenge arises: how do you access user-mode memory space while in kernel mode?

## Identifying the Current Context

First, we need a kernel debugger, such as **WinDbg**, to perform tracing in kernel mode. We can check the current thread and process using the `!thread` command:

kd> !thread

THREAD ffffa68179f35140  Cid 0000.0000  Teb: 0000000000000000 Win32Thread: 0000000000000000 RUNNING on processor 2

Not impersonating

DeviceMap                 ffff80062704a6c0

Owning Process            fffff80246b24a00       Image:         Idle

Attached Process          ffffd807eee7a080       Image:        **System**

...

At this moment, our current process is **Idle**, and the attached process is **System**.

## Locating the Target Process

Our target process is an already running `notepad.exe`. We use the `!process` command to find its address:

kd> !process 0 0 notepad.exe

PROCESS ffffd807f52a6080

SessionId: 1  Cid: 18f4    Peb: edc3026000  ParentCid: 11b4
    
DirBase: 0938e000  ObjectTable: ffff80062b0f3a00  HandleCount: 245.
    
Image: notepad.exe

## Switching the Process Context

Next, we must change the current process context to access the user-mode memory space of the target. We execute the following command:

kd> .process /i /p ffffd807f52a6080

You need to continue execution (press 'g' <enter>) for the context
to be switched. When the debugger breaks in again, you will be in
the new process context.

**Parameter Meanings:**

-   **/i (Invasive):** This is the most crucial step. It makes the target process the current execution environment. Since certain memory operations must occur within the target process's thread context, this flag uses a "break-in" style interrupt to stop the target machine when it next runs that process. Note: after using `/i`, you usually need to issue the `g` (go) command.
    
-   **/p (Page Table):** This switches the page directory by pointing the processor's **CR3** register to the page directory of the specified process.

At this stage, the system has not yet truly switched to the target process. To complete the switch, execute:

kd> g

## Confirming the Context Switch

Once the debugger breaks in again, we can verify the context using `!thread`:

kd> !thread

...

Owning Process            ffffd807eee7a080       Image:         System

Attached Process          ffffd807f52a6080       Image:         **notepad.exe**

...

As shown, the attached process is now **notepad.exe**, indicating we have successfully attached to the target process.

## Loading Symbols and Setting Breakpoints

The next **critical** step is loading the symbols for the target process (ensure your symbol path is set correctly beforehand):

kd> .reload /f notepad.exe

Verify the module is loaded using `lm`:

kd> lm

start             end                 module name

00007ff7`a9f60000 00007ff7`a9f98000   notepad    (pdb symbols) ...

...

Now, we can list the symbols in the target process using the `x` command:

kd> x notepad!*

00007ff7`a9f68408 notepad!ShowOpenSaveDialog (void)

...

Set an unresolved breakpoint (`bu`) on the function of interest:

 kd> bu notepad!ShowOpenSaveDialog
 
 kd> g

## Analyzing the Stack and Parameters

Once the breakpoint is hit, use the `k` command to view the call stack:

kd> k

Child-SP          RetAddr               Call Site

00 000000ed`c2efeb18 00007ff7`a9f68857     notepad!ShowOpenSaveDialog

01 000000ed`c2efeb20 00007ff7`a9f69147     notepad!InvokeOpenDialog+0xc3

...

To see how the function was called in the parent function, use the `ub` (Unassemble Backward) command with the return address:

kd> ub 00007ff7`a9f68857  L10

...

00007ff7`a9f68841 4c8b05707d0200  mov     r8,qword ptr [notepad!szOpenCaption (00007ff7`a9f905b8)]

00007ff7`a9f68848 4c8bcf          mov     r9,rdi

00007ff7`a9f6884b 488b55f0        mov     rdx,qword ptr [rbp-10h]

00007ff7`a9f6884f 488bce          mov     rcx,rsi

00007ff7`a9f68852 e8b1fbffff      call    notepad!ShowOpenSaveDialog (00007ff7`a9f68408)

According to the **x64 Microsoft Calling Convention**, `notepad!ShowOpenSaveDialog` uses the following registers for its first four parameters:

-   **RCX** - 1st argument
-   **RDX** - 2nd argument
-   **R8** - 3rd argument
-   **R9** - 4th argument
    
From the disassembly, it is clear that the third parameter (**R8**) for `notepad!ShowOpenSaveDialog` is a string pointer (caption).



