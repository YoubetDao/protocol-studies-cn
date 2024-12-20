# 以太坊虚拟机 (EVM)

The Ethereum Virtual Machine (EVM) is the heart of the Ethereum World Computer. It performs the crucial computations needed to finalise transactions, permanently storing the results on the blockchain. This article explores the EVM's role in the Ethereum ecosystem and how it works.

以太坊虚拟机 (EVM) 是以太坊世界计算机的核心。它执行完成交易所需的计算，并将结果永久存储在区块链上。本文探讨了 EVM 在以太坊生态系统中的作用及其工作原理。

## 以太坊状态机

As the EVM processes transactions, it alters the overall state of Ethereum. In that regard, Ethereum can be viewed as a **state machine**.

当 EVM 处理交易时，它会改变以太坊的整体状态。从这个角度来看，以太坊可以被视为一个**状态机**。

In computer science, **state machine** is an abstraction used to model the behavior of a system. It illustrates how a system can be represented by a set of distinct states and how inputs can drive changes in the state.

在计算机科学中，**状态机**是一个用于模拟系统行为的抽象概念。状态机可以用 组不同的状态以及驱动状态变化的输入来描述整个系统。

A familiar example is a vending machine, an automated system for dispensing products upon receiving payment.

一个常见的例子是自动售货机，这是一个在收到付款后自动分发产品的系统。

We can model a vending machine existing in three distinct states: idle, awaiting user selection, and dispensing a product. Inputs such as coin insertion or product selection trigger transitions between these states, as depicted in the state diagram:

我们可以将自动售货机建模为三种不同的状态：空闲、等待选择和分发产品。输入例如投币或选择会触发这些状态之间的转换，如下图所示：

![Vending machine](./images/evm/vending-machine.gif)

Let's formally define the components of a state machine:

让我们正式定义状态机的组成部分：

1. **State ($S$)**: A State represents distinct conditions or configurations that a system can be in at a given point in time.
   For the vending machine, possible states are:

$$ S\in \set {Idle, Selection, Dispensing} $$

1. **状态 ($S$)**: 状态表示系统在某一特定时间点的不同条件或配置。对于自动售货机，可能的状态有：

$$ S\in \\{空闲, 等待选择, 分发产品\\} $$

2. **Inputs ($I$)**: Inputs are actions, signals, or changes in the system's environment. Input triggers the **state transition function**.
   For the vending machine, possible inputs include:

3. **输入 ($I$)**: 输入是系统环境中的动作、信号或变化。输入触发**状态转移函数**。对于自动售货机，可能的输入包括：

$$ I\in \\{投币, 选择, 取货\\} $$

3. **State transition function ($\Upsilon$)**: State transition function defines how the system transitions from one state to another (or back to the same state) based on an input and its current state. Essentially, it determines how the system evolves in response to inputs.

4. **状态转移函数 ($\Upsilon$)**: 状态转移函数定义了系统如何基于输入和当前状态从一个状态转移到另一个状态（或回到同一个状态），这决定了系统如何响应输入。

$$\Upsilon (S,I) \Longrightarrow S'  $$

> Where S' = next state, S= current state, and I= input.

> 其中 $S'$ 是下一个状态，$S$ 是当前状态，$I$ 是输入。

Example state transition for the vending machine:

$$\Upsilon (Idle,InsertCoin) \Longrightarrow Selection $$
$$\Upsilon (Selection,SelectDrink) \Longrightarrow Dispensing $$
$$\Upsilon (Idle,SelectDrink) \Longrightarrow Idle $$

状态转移示例：

$$\Upsilon (空闲,投币) \Longrightarrow 等待选择 $$
$$\Upsilon (等待选择,选择) \Longrightarrow 分发产品 $$
$$\Upsilon (空闲,选择) \Longrightarrow 空闲 $$

Notice in the last case, the current state transitions back to itself.

注意在最后一种情况下，当前状态会回到自身。

### 作为状态机的以太坊

Ethereum, as a whole, can be viewed as a **transaction-based state machine**. It receives transactions as input and transitions into a new state. The current state of Ethereum is referred to as the **world state**.

Consider a simple Ethereum application - an [NFT](https://ethereum.org/en/nft/) marketplace.

整个以太坊系统可以被视为一个**基于交易的**状态机。它接收交易作为输入，并过渡到新的状态。以太坊的当前状态被称为**世界状态**。

让我们考虑一个简单的以太坊应用程序 —— 一个[NFT](https://ethereum.org/en/nft/)市场。

In the current world state **S3** (green), Alice owns an NFT. The animation below shows a transaction transferring ownership to you (**S3** ➡️ **S4**). Similarly, selling the NFT back to Alice would transition the state to **S5**:

在当前世界状态 **S3**（绿色）中，Alice 拥有一个 NFT。动画显示了一个将 NFT 所有权转移给你的交易（**S3** ➡️ **S4**）。同样，将 NFT 再卖回给 Alice 会将其状态转移到 **S5**：

![Ethereum state machine](./images/evm/ethereum-state-machine.gif)

Notice the current world state is animated _as a pulsating green bubble_.

注意当前世界状态会被表示为 _跳动的绿色气泡_。

In the figure above, each transaction is committed to a new state. However, in reality, a group of transactions is bundled into a **block**, and the resulting state is added to the chain of previous states. It must be apparent now why this technology is called **blockchain**.

在上述图中，每个交易都提交到一个新的状态。然而，实际上，一组交易被捆绑到一个**区块**中，并且结果状态被添加到先前状态的链中。这就是为什么这项技术被称为**区块链**的原因。

Considering the definition of the state transition function, we draw the following conclusion:

根据状态转移函数的定义，我们得出以下结论：

> ℹ️ Note
> **EVM is the state transition function of the Ethereum state machine. It determines how Ethereum transitions into a new (world) state based on input (transactions) and current state.**

> ℹ️ 注意
> **EVM 就是以太坊状态机的状态转移函数。它决定了以太坊如何根据输入（交易）和当前状态过渡到新的（世界）状态。**

In Ethereum, the world state is essentially a mapping of 20-byte addresses to account states.

在以太坊中，世界状态本质上是一个 20 字节地址到账户状态的映射。

![Ethereum world state](./images/evm/ethereum-world-state.jpg)

Each account state consists of various components such as storage, code, balance among other data and is associated with a specific address.

Ethereum has two kinds of accounts:

每个账户状态包含各种组件，例如存储、代码、余额等，并与其特定地址相关联。

Ethereum 有两种账户：

- **External account:** An account [controlled by an associated private key](/wiki/Cryptography/ecdsa.md) and empty EVM code.
- **Contract account:** An account controlled by an associated non-empty EVM code. The EVM code as part of such an account is colloquially known as a _smart contract._

- **外部账户：** 由与其关联的私钥控制的账户，且其 EVM 代码为空。
- **合约账户：** 由与其关联的非空 EVM 代码控制的账户。这段 EVM 代码是合约的一部分，通常被称为 _智能合约_。

Refer [Ethereum data structures](wiki/EL/data-structures.md) for details on how the world state is implemented.

参考 [Ethereum 数据结构](/wiki/EL/data-structures.md) 了解更多关于世界状态实现的信息。

## Virtual machine paradigm

## 虚拟机范式

Given our grasp of state machines, the next challenge is **implementation**.

在我们理解了状态机的概念之后，下一个挑战是**实现它**。

Software needs conversion to the target processor's machine language (Instruction Set Architecture, ISA) for execution. This ISA varies across hardware (e.g., Intel vs. Apple silicon). Modern software also relies on the host operating system for memory management and other essentials.

软件需要转换为目标处理器的机器语言（指令集，ISA）才能执行。这种 ISA 因硬件而异（例如，Intel 与 Apple silicon）。现代软件还依赖主机操作系统进行内存管理和其他基本功能。

Ensuring functionality within a fragmented ecosystem of diverse hardware and operating systems is a major hurdle. Traditionally, software had to be compiled into native binaries for each specific target platform:

在由不同硬件和操作系统组成的分散生态系统中确保功能正常是一个主要挑战。传统上，软件必须为每个特定的目标平台编译成原生二进制文件：

![Platform dependent execution](./images/evm/platform-dependent-execution.jpg)

To address this challenge, a two-part solution is employed.

为了解决这个挑战，虚拟机采用的解决方案分为两个部分。

First, the implementation targets a **virtual machine**, an abstraction layer. Source code is compiled into **bytecode**, a sequence of bytes representing instructions. Each bytecode maps to a specific operation the virtual machine executes.

首先，讨论面向**虚拟机**这个抽象层的实现。虚拟机会将源代码被编译成**字节码**，这是一个表示指令的字节序列。每个字节码都映射到虚拟机执行的特定操作。

The second part involves a platform-specific virtual machine that translates the bytecode into native code for execution.

第二部分涉及特定平台的虚拟机，它将字节码转换为可执行的原生代码。

This offers two key benefits: portability (bytecode runs on different platforms without recompiling) and abstraction (separates hardware complexities from software). Developers can thus write code for a single, virtual machine:

这提供了两个关键优势：可移植性（字节码可以在不同平台上运行而无需重新编译）和抽象性（将硬件复杂性与软件分离）。因此，开发人员只需要为单一的虚拟机编写代码：

![Virtual machine paradigm](./images/evm/virtual-machine-paradigm.jpg)

[JVM](https://en.wikipedia.org/wiki/Java_virtual_machine) for Java and LuaVM for Lua are popular examples of Virtual machines. They create platform-neutral bytecode, enabling code to run on various systems without recompiling.

Java 的 [JVM](https://en.wikipedia.org/wiki/Java_virtual_machine) 和 Lua 的 LuaVM 是虚拟机的典型例子。它们创建平台无关的字节码，使代码能够在各种系统上运行而无需重新编译。

## EVM

The virtual machine concept serves as an abstraction. Ethereum Virtual Machine (EVM) is a _specific_ software implementation of this abstraction. The anatomy of the EVM is described below:

In computer architecture, a word refers to a fixed-size unit of data that the CPU can process at once. EVM has a word size of **32 bytes**.

虚拟机是计算机架构中的一个抽象概念，而以太坊虚拟机（EVM）是这个抽象的一个具体软件实现。下面描述了 EVM 的架构：

在计算机架构中，字（word）指的是 CPU 一次可以处理的固定大小的数据单元。EVM 的字大小为 **32 字节**。

![EVM anatomy](./images/evm/evm-anatomy.jpg)

_For clarity, the figure above simplifies the Ethereum state. The actual state includes additional elements like Message Frames and Transient Storage._

_为了清晰起见，上图简化了以太坊状态。实际状态包括消息帧和临时存储等额外元素。_

In the anatomy described above, EVM is shown to be manipulating the storage, code, and balance of an account instance.

In a real-world scenario, EVM may execute transactions involving multiple accounts (each with independent storage, code, and balance) enabling complex interactions on Ethereum.

在上述架构图中，可以看到 EVM 正在操作账户实例的存储、代码区和余额。

在实际场景中，EVM 可能会执行涉及多个账户的交易（每个账户都有独立的存储、代码和余额），从而实现以太坊上的复杂交互。

With a better grasp of virtual machines, let's extend our definition:

> ℹ️ Note
> EVM is the state transition function of the Ethereum state machine. It determines how Ethereum transitions into a new (world) state based on
> input (transactions) and current state. **It is implemented as a virtual machine so that it can run on any platform, independent of the
> underlying hardware.**

在更好地理解了虚拟机之后，让我们扩展我们的定义：

> ℹ️ 注意
> EVM 就是以太坊状态机的状态转移函数。它决定了以太坊如何根据输入（交易）和当前状态过渡到新的（世界）状态。**它被实现为虚拟机，因此可以在任何平台上运行，不依赖于底层硬件。**

## EVM bytecode

EVM bytecode is a representation of a program as a sequence of [**bytes** (8 bits).](https://en.wikipedia.org/wiki/Byte) Each byte within the bytecode is either:

- an instruction known as **opcode**, or
- input to an opcode known as **operand**.

- 一个称为 **操作码** 的指令，或者
- 一个称为 **操作数** 的操作码的输入。

![Binary bytecode](./images/evm/opcode-binary.jpg)

For brevity, EVM bytecode is commonly expressed in [**hexadecimal**](https://en.wikipedia.org/wiki/Hexadecimal) notation:

为了简洁起见，EVM 字节码通常用 [**十六进制**](https://en.wikipedia.org/wiki/Hexadecimal) 表示：

![Hexadecimal bytecode](./images/evm/opcode-hex.jpg)

To further enhance comprehension, opcodes have human-readable mnemonics. This simplified bytecode, called **EVM assembly**, is the lowest human-readable form of EVM code:

为了进一步增强可读性，操作码有可读的助记符。这种简化的字节码被称为 **EVM 汇编**，是人类可读的最低层次的 EVM 代码表示形式：

![EVM Assembly](./images/evm/opcode-assembly.jpg)

Identifying opcodes from operands is straightforward. Currently, only `PUSH*` opcodes have operands (this might change with [EOF](https://eips.ethereum.org/EIPS/eip-7569)). `PUSHX` defines operand length (X bytes after PUSH).

Select Opcodes used in this discussion:

区分操作数和操作码是很直观的。目前，只有 `PUSH*` 操作码有对应的操作数（这可能会随着 [EOF](https://eips.ethereum.org/EIPS/eip-7569) 的实施而改变）。`PUSHX` 通过 X 定义了操作数的长度（PUSH 操作码后的 X 字节）。

下面列出了本文中使用到的操作码：

| Opcode | Name     | Description                                        |
| ------ | -------- | -------------------------------------------------- |
| 60     | PUSH1    | Push 1 byte on the stack                           |
| 01     | ADD      | Add the top 2 values of the stack                  |
| 02     | MUL      | Multiply the top 2 values of the stack             |
| 39     | CODECOPY | Copy code running in current environment to memory |
| 51     | MLOAD    | Load word from memory                              |
| 52     | MSTORE   | Store word to memory                               |
| 53     | MSTORE8  | Store byte to memory                               |
| 59     | MSIZE    | Get the byte size of the expanded memory           |
| 54     | SLOAD    | Load word from storage                             |
| 55     | SSTORE   | Store word to storage                              |
| 56     | JUMP     | Alter the program counter                          |
| 5B     | JUMPDEST | Mark destination for jumps                         |
| f3     | RETURN   | Halt execution returning output data               |

Refer [Appendix H of Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf) for a comprehensive list.

请参考 [Yellow Paper 的附录 H](https://ethereum.github.io/yellowpaper/paper.pdf) 获取完整列表。

> ℹ️ Note
> [EIPs](https://eips.ethereum.org/) can propose EVM modifications. For instance, [EIP-1153](https://eips.ethereum.org/EIPS/eip-1153) introduced `TSTORE`, and `TSTORE` opcodes.

> ℹ️ 注意
> 可以通过 [EIPs](https://eips.ethereum.org/) 提出对 EVM 的修改。例如，[EIP-1153](https://eips.ethereum.org/EIPS/eip-1153) 引入了 `TSTORE` 操作码。

Ethereum clients such as [geth](https://github.com/ethereum/go-ethereum) implement the [EVM specifications](https://github.com/ethereum/execution-specs). This ensures all nodes agree on how transactions alter the system's state, creating a uniform execution environment across the network.

Ethereum 客户端（如 [geth](https://github.com/ethereum/go-ethereum)）实现了 [EVM 规范](https://github.com/ethereum/execution-specs)。这确保了所有节点在交易如何改变系统状态方面达成一致，从而在网络中创建了一个统一的执行环境。

We have covered **what** EVM is, let's explore **how** it works.

我们已经讨论了**EVM 是什么**，现在让我们探索**它是如何工作**的。

## 栈(Stack)

Stack is a simple data structure with two operations: **PUSH** and **POP**. Push adds an item to top of the stack, while pop removes the top-most item. Stack operates on Last-In-First-Out (LIFO) principle - the last element added is the first removed. If you try to pop from an empty stack, a **stack underflow error** occurs.

栈是一种简单的数据结构，具有两个操作：**PUSH** 和 **POP**。Push 将一个项添加到栈顶，而 pop 则移除栈顶的项。栈遵循后进先出（LIFO）原则——最后添加的元素是第一个被移除的。如果尝试从空栈中弹出，将发生**栈下溢错误**。

![EVM stack](./images/evm/stack.gif)

> The EVM stack has a maximum size of 1024 items.

During bytecode execution, EVM stack functions as a _scratchpad_: opcodes consume data from the top and push results back (See the `ADD` opcode below). Consider a simple addition program:

> EVM 栈的最大大小为 1024 项。

在字节码执行期间，EVM 栈作为**临时存储**使用：操作码从栈顶消耗数据，并将其结果推回栈顶（见下面的 `ADD` 操作码）。考虑一个简单的加法程序：

![EVM stack addition](./images/evm/stack-addition.gif)

Reminder: All values are in hexadecimal, so `0x06 + 0x07 = 0x0d (decimal: 13)`.

Let's take a moment to celebrate our first EVM assembly code 🎉.

> 提示：所有值都是十六进制的，所以 `0x06 + 0x07 = 0x0d (十进制: 13)`。

让我们花点时间庆祝一下我们编写的第一行 EVM 汇编代码 🎉。

## 程序计数器(Program counter)

In the example above, the values on the left of the assembly code represent the byte offset (starting at 0) of each opcode within the bytecode:

| Bytecode | Assembly | Length of Instruction in bytes | Offset in hex |
| -------- | -------- | ------------------------------ | ------------- |
| 60 06    | PUSH1 06 | 2                              | 00            |
| 60 07    | PUSH1 07 | 2                              | 02            |
| 01       | ADD      | 1                              | 04            |

在���述示例中，汇编代码左侧的值表示字节码中每个操作码的**字节偏移量**（从 0 开始）：

| 字节码 | 汇编指令 | 指令长度（字节） | 字节偏移量（十六进制） |
| ------ | -------- | ---------------- | ---------------------- |
| 60 06  | PUSH1 06 | 2                | 00                     |
| 60 07  | PUSH1 07 | 2                | 02                     |
| 01     | ADD      | 1                | 04                     |

EVM **program counter** stores the byte offset of the next opcode (highlighted) to be executed.

EVM 用**程序计数器**存储下一个要执行的操作码的字节偏移量（高亮显示）。

![EVM program counter](./images/evm/program-counter.gif)

The `JUMP` opcode directly sets the program counter, enabling dynamic control flow and contributing to the EVM's [Turing completeness](https://en.wikipedia.org/wiki/Turing_completeness) by allowing flexible program execution paths.

`JUMP` 操作码通过直接设置程序计数器，启用动态控制流从而允许灵活的程序执行路径并使 EVM 具有 [图灵完备性](https://en.wikipedia.org/wiki/Turing_completeness)。

![EVM JUMP opcode](./images/evm/jump-opcode.gif)

The code runs in an infinite loop, repeatedly adding 7. It introduces two new opcodes:

- **JUMP**: Sets the program counter to stack top value (02 in our case), determining the next instruction to execute.
- **JUMPDEST**: Marks the destination of a jump operation, ensuring intended destinations and preventing unwanted control flow disruptions.

这段代码正在执行一个死循环，并不断地往栈顶添加元素 7。它引入了两个新操作码：

- **JUMP**: 将程序计数器设置为栈顶值（在我们的例子中是 02），确定下一个要执行的指令。
- **JUMPDEST**: 标记跳转操作的目的地，确保预期的目的地。


> High level languages like [Solidity](https://soliditylang.org/) leverage jumps for constructs like if conditions, for loops, and internal functions calls.

> 高级别语言（如 [Solidity](https://soliditylang.org/)）利用 `JUMP` 和 `JUMPDEST` 实现诸如条件、循环和内部函数调用等语句。

## 汽油费（Gas）

Our innocent little program may seem harmless. However, infinite loops in EVM pose a significant threat: they can **devour resources**, potentially causing network [**DoS attacks**.](https://en.wikipedia.org/wiki/Denial-of-service_attack)

我们的小程序可能看起来无害。但是其中的无限循环对 EVM构成了重大威胁：它们可以**耗尽资源**，可能导致网络 [**拒绝服务攻击**](https://en.wikipedia.org/wiki/Denial-of-service_attack)。


The EVM's **gas** mechanism tackles such threats by acting as a currency for computational resources. Gas costs are [designed to mirror](https://web.archive.org/web/20170904200443/https://docs.google.com/spreadsheets/d/15wghZr-Z6sRSMdmRmhls9dVXTOpxKy8Y64oy9MvDZEQ/edit#gid=0) the limitations of hardware, such as storage capacity or processing power. Transactions pay gas in **Ether (ETH)** to use the EVM, and if they run out of gas before finishing (like an infinite loop), the EVM halts them to prevent resource hogging.

EVM 的 **gas** 机制通过充当计算使用资源的货币来应对这种威胁。gas 费基于使用的硬件资源（如存储容量或计算能力）来计算。交易通过以太坊代币支付 gas 费以使用 EVM，如果它们在完成之前耗尽 gas 费（如无限循环），EVM 会停止该交易继续执行以防止资源耗尽。

This protects the network from getting clogged by resource-intensive or malicious activities. Since gas restricts computations to a finite number of steps, the EVM is considered **quasi Turing complete**.

这保护了网络免受资源密集型或恶意攻击的影响。由于 gas 费限制了计算步骤的数量，EVM 被认为是**准图灵完备**的。

![EVM Gas](./images/evm/evm-gas.gif)

Our example assumed 1 unit of gas per opcode for simplicity, but the actual cost varies based on complexity. The core concept, however, remains unchanged.

Refer [Appendix G of Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf) for specific gas costs.

在我们的示例中，假设每个操作码消耗 1 单位的 gas 以简化计算，但实际 gas 成本会根据操作码的复杂性而变化。不过核心理念是一致的。

请参考 [Yellow Paper 的附录 G](https://ethereum.github.io/yellowpaper/paper.pdf) 获取具体的 gas 费。

## 内存（Memory）

EVM memory is a byte array of $2^{256}$ (or [practically infinite](https://www.talkcrypto.org/blog/2019/04/08/all-you-need-to-know-about-2256/)) bytes . All locations in memory are well-defined initially as zero.

EVM 内存是一个字节数组，大小为 $2^{256}$（或 [实际无限](https://www.talkcrypto.org/blog/2019/04/08/all-you-need-to-know-about-2256/)）字节。所有内存位置会初始化为零。

![EVM Memory](./images/evm/evm-memory.gif)

Unlike stack, which provides data to individual instructions, memory stores ephemeral data that is relevant to the entire program.

与用来存储每个指令所需操作数据的栈不同，内存存储与整个程序相关的临时数据。

### 写入内存

`MSTORE` takes two values from the stack: an address **offset** and a 32-byte **value**. It then writes the value to memory at the specified offset.

`MSTORE` 从栈中获取两个值：一个**偏移量**和一个 32 字节的**值**。然后，它将该值写入指定偏移量的内存中。

![MSTORE](./images/evm/mstore.gif)

`MSIZE` reports currently used memory size (in bytes) on the stack (32 bytes or 20 hex in this case).

`MSTORE8` takes same arguments as `MSTORE`, but writes 1 byte instead of 1 word.

`MSIZE` 报告当前使用的内存大小（以字节为单位）并压入栈中。

`MSTORE8` 与 `MSTORE` 类似，但只写入 1 字节。

![MSTORE8](./images/evm/mstore8.gif)

Notice: When writing 07 to memory, the existing value (06) remains unchanged. It's written to the adjacent byte offset.

The size of active memory is still 1 word.

注意：当写入 07 到内存时，现有的值（06）保持不变。07 会被写入相邻的字节。

当前占用的内存大小仍然是 1 个字。

### 内存扩展 (Memory expansion)

In EVM, memory is dynamically allocated in multiples of 1 word "pages". Gas is charged for the number of pages expanded.

在 EVM 中，内存是按 1 个字（32 字节）的倍数动态分配的。EVM 也会基于扩展的页数收取 gas 费。

![Memory expansion](./images/evm/memory-expansion.gif)

Writing a word at a 1-byte offset overflows the initial memory page, triggering an expansion to 2 words (64 bytes or 0x40).

在一个字节偏移量的位置写入一个字，会导致溢出初始内存页，触发内存扩展到 2 个字（64 字节或 0x40）。

### 读取内存 (Reading from memory)

`MLOAD` reads a value from memory and pushes it onto the stack.

`MLOAD` 指令从内存中读取一个值并压入栈中。

![MLOAD](./images/evm/mload.gif)

EVM doesn't have a direct equivalent to `MSTORE8` for reading. You must read the entire word using `MLOAD` and then extract the desired byte using a [mask](<https://en.wikipedia.org/wiki/Mask_(computing)>).

EVM 没有直接等价的 `MSTORE8` 用于读取。必须使用 `MLOAD` 读取整个字，然后使用 [掩码](<https://en.wikipedia.org/wiki/Mask_(computing)>) 提取所需的字节。

> EVM memory is shown as blocks of 32 bytes to illustrate how memory expansion works. In reality, it is a seamless sequence of bytes, without any inherent divisions or blocks.

> EVM 内存被显示为 32 字节的块，以说明内存扩展的工作原理。实际上，它是一个连续的字节序列，没有任何固有的分隔或块。

## Storage

Storage is designed as a **word-addressed word array**. Unlike memory, storage is associated with an Ethereum account and is **persisted** across transactions as part of the world state.

Storage 被设计为一个**以字为地址的字数组**。与 Memory 不同，Storage 与以太坊账户相关联，并作为世界状态的一部分在交易之间**持久保存**。

![EVM Storage](./images/evm/evm-storage.jpg)

Storage can only be accessed via the code of its associated account. External accounts don't have code and therefore cannot access their own storage.

Storage 只能通过其关联账户的代码来访问。外部账户没有代码，因此无法访问自己的 Storage。

## Writing to storage

`SSTORE` takes two values from the stack: a storage **slot** and a 32-byte **value**. It then writes the value to storage of the account.

`SSTORE` 从栈中获取两个值：一个存储**槽位**和一个 32 字节的**值**。然后它将该值写入账户的 Storage 中。

![EVM Storage write](./images/evm/sstore.gif)

We've been running a contract account's bytecode all this time. Only now we see the account and the world state, and it matches the code inside the EVM.

在此之前，我们一直在关注合约账户的字节码执行过程。现在当我们看到账户和世界状态时，就能发现它们与 EVM 内部运行的代码是完全对应的。

Again, it's important to note that storage is not part of the EVM itself, rather the currently executing contract account.

再次强调，Storage 不是 EVM 本身的一部分，而是属于当前执行的合约账户。

The example above shows only a small section of the account's storage. Like memory, all the values in storage are well-defined as zero.

上面的例子只展示了账户 Storage 的一小部分。与内存一样，Storage 中的所有值初始都被定义为零。

### 读取存储 (Reading from storage)

`SLOAD` takes the storage **slot** from the stack and loads its value back onto it.

`SLOAD` 从栈中获取存储**槽位**，并将其值加载回栈中。

![EVM Storage read](./images/evm/sload.gif)

Notice that the storage value persists between examples, demonstrating its persistence within the world state. Since the world state is replicated across all nodes, storage operations are gas expensive.

注意存储值在示例之间保持不变，这展示了它在世界状态中的持久性。由于世界状态需要在所有节点间复制，存储操作的 gas 费用很高。

> ℹ️ Note
> Check out the wiki on [transaction](/wiki/EL/transaction.md) to see EVM in action.

> ℹ️ 注意
> 查看 [transaction](/wiki/EL/transaction.md) 维基页面以了解 EVM 的实际运行。

## Wrapping up

Developers rarely write EVM assembly code directly unless performance optimization is crucial. Instead, most developers work with higher-level languages like [Solidity](https://soliditylang.org/), which is then compiled into bytecode.

除非性能优化至关重要，否则开发人员很少直接编写 EVM 汇编代码。相反，大多数开发人员使用像 [Solidity](https://soliditylang.org/) 这样的高级语言，然后将其编译成字节码。

Ethereum is a continuously evolving protocol and while the fundamentals we've discussed will remain largely relevant, following [Ethereum Improvement Proposals (EIPs)](https://eips.ethereum.org/) and [network upgrades](https://ethereum.org/history) is encouraged to stay informed of the latest developments in the Ethereum ecosystem.

以太坊是一个持续发展的协议，虽然我们讨论的基础知识将在很大程度上保持不变，但建议关注 [以太坊改进提案(EIPs)](https://eips.ethereum.org/) 和 [网络升级](https://ethereum.org/history)，以了解以太坊生态系统的最新发展。

## 以太坊虚拟机升级(EVM upgrades)

While Ethereum protocol undergoes many changes in each upgrade, changes in EVM are rather subtle. Major change in EVM might break contracts and languages, requiring keeping multiple versions of EVM which introduces a lot of complexity overhead. There are still certain upgrades done on EVM itself like new opcodes or changes to existing ones which don't break their logic. Some examples are EIPs like [1153](https://eips.ethereum.org/EIPS/eip-1153), [4788](https://eips.ethereum.org/EIPS/eip-4788), [5000](https://eips.ethereum.org/EIPS/eip-5000), [5656](https://eips.ethereum.org/EIPS/eip-5656) and [6780](https://eips.ethereum.org/EIPS/eip-6780). These are proposing to add new opcodes except the last one which is especially interesting because it's neutralizing `SELFDESTRUCT` opcode without breaking compatibility. Another important upgrade to EVM which would mark rather a major change is [EOF](https://notes.ethereum.org/@ipsilon/mega-eof-specification). It creates a format to bytecode which EVM can understand and process more easily, it encompasses various EIPs and has been discussed and polished for quite some time.

虽然以太坊协议在每次升级中都会经历许多变化，但 EVM 的变化相对较小。因为 EVM 的重大变更可能会破坏合约和语言的兼容性，需要维护多个版本的 EVM，这会带来大量的复杂性开销。EVM 本身仍然会进行一些不破坏其逻辑的升级，比如新的操作码或对现有操作码的修改。一些例子是像 [1153](https://eips.ethereum.org/EIPS/eip-1153)、[4788](https://eips.ethereum.org/EIPS/eip-4788)、[5000](https://eips.ethereum.org/EIPS/eip-5000)、[5656](https://eips.ethereum.org/EIPS/eip-5656) 和 [6780](https://eips.ethereum.org/EIPS/eip-6780) 这样的 EIP。除了最后一个特别有趣的 EIP（它在不破坏兼容性的情况下中和了 `SELFDESTRUCT` 操作码）之外，这些都在提议添加新的操作码。另一个标志着重大变革的重要 EVM 升级是 [EOF](https://notes.ethereum.org/@ipsilon/mega-eof-specification)。它为字节码创建了一种 EVM 可以更容易理解和处理的格式，涵盖了各种 EIP，并且已经讨论和完善了相当长的时间。

## 资源

### 状态机和计算理论

- 📝 Mark Shead, ["Understanding State Machines."](https://medium.com/free-code-camp/state-machines-basics-of-computer-science-d42855debc66) • [archived](https://web.archive.org/web/20210309014946/https://medium.com/free-code-camp/state-machines-basics-of-computer-science-d42855debc66)
- 🎥 Prof. Harry Porter, ["Theory of computation."](https://www.youtube.com/playlist?list=PLbtzT1TYeoMjNOGEiaRmm_vMIwUAidnQz)
- 📘 Michael Sipser, ["Introduction to the Theory of Computation."](https://books.google.com/books/about/Introduction_to_the_Theory_of_Computatio.html?id=4J1ZMAEACAAJ)
- 🎥 Shimon Schocken et al., ["Build a Modern Computer from First Principles: From Nand to Tetris."](https://www.coursera.org/learn/build-a-computer)

### EVM 学习资源

下面根据不同的学习阶段和目标汇总了一些 EVM 相关的学习资料。

#### EVM 基础

- 🎥 Whiteboard Crypto, ["EVM: An animated non-technical introduction."](https://youtu.be/sTOcqS4msoU)
- 📝 Vasa, [Getting Deep Into EVM: How Ethereum Works Backstage](https://medium.com/swlh/getting-deep-into-evm-how-ethereum-works-backstage-ab6ad9c0d0bf)
- 📝 Zaryab Afser, [The ABCs of Ethereum Virtual Machine](https://www.decipherclub.com/the-abcs-of-ethereum-virtual-machine/)
- 📝 Preethi, [EVM Tweet Thread](https://twitter.com/iam_preethi/status/1483459717670309895)
- 📝 Decipher Club, [EVM learning resources based on your level of expertise](https://www.decipherclub.com/evm-learning-resources/)

#### 理解 EVM 架构和核心组件

- 📝 Gavin Wood, ["Ethereum Yellow Paper."](https://ethereum.github.io/yellowpaper/paper.pdf)
- 📝 Ethereum Book, [Chapter 13, Ethereum Book](https://cypherpunks-core.github.io/ethereumbook/13evm.html?ref=decipherclub.com)
- 📘 Andreas M. Antonopoulos & Gavin Wood, ["Mastering Ethereum."](https://github.com/ethereumbook/ethereumbook)
- 🎥 Jordan McKinney, ["Ethereum Explained: The EVM."](https://www.youtube.com/watch?v=kCswGz9naZg)
- 📝 LeftAsExercise, ["Smart contracts and the Ethereum virtual machine."](https://leftasexercise.com/2021/08/08/q-smart-contracts-and-the-ethereum-virtual-machine/) • [archived](https://web.archive.org/web/20230324200211/https://leftasexercise.com/2021/08/08/q-smart-contracts-and-the-ethereum-virtual-machine/)
- 📝 Femboy Capital, ["A Playdate with the EVM."](https://femboy.capital/evm-pt1) • [archived](https://web.archive.org/web/20221001113802/https://femboy.capital/evm-pt1)
- 🎥 Alex, [EVM - Some Assembly Required](https://www.youtube.com/watch?v=yxgU80jdwL0)

#### EVM 深入探讨

- 📝 Takenobu Tani, [EVM illustrated](https://github.com/takenobu-hs/ethereum-evm-illustrated)
- 📝 Shafu, ["EVM from scratch."](https://evm-from-scratch.xyz/)
- 📝 NOXX, ["3 part series: EVM Deep Dives - The Path to Shadowy Super Coder."](https://noxx.substack.com/p/evm-deep-dives-the-path-to-shadowy) • [archived](https://web.archive.org/web/20240106034644/https://noxx.substack.com/p/evm-deep-dives-the-path-to-shadowy)
- 📝 OpenZeppelin, ["6 part series: Deconstructing a Solidity."](https://blog.openzeppelin.com/deconstructing-a-solidity-contract-part-i-introduction-832efd2d7737) • [archived](https://web.archive.org/web/20240121025651/https://blog.openzeppelin.com/deconstructing-a-solidity-contract-part-i-introduction-832efd2d7737)
- 📝 TrustLook, ["Understand EVM bytecode."](https://blog.trustlook.com/understand-evm-bytecode-part-1/) • [archived](https://web.archive.org/web/20230603080857/https://blog.trustlook.com/understand-evm-bytecode-part-1/)
- 📝 Degatchi, ["A Low-Level Guide To Solidity's Storage Management."](https://degatchi.com/articles/low_level_guide_to_soliditys_storage_management) • [archived](https://web.archive.org/web/20231202105650/https://degatchi.com/articles/low_level_guide_to_soliditys_storage_management/)
- 📝 Zaryab Afser, ["Journey of smart contracts from Solidity to Bytecode"](https://www.decipherclub.com/ethereum-virtual-machine-article-series/)
- 🎥 Ethereum Engineering Group, [EVM: From Solidity to byte code, memory and storage](https://www.youtube.com/watch?v=RxL_1AfV7N4&t=2s)
- 📝 Trust Chain, [7 part series about how Solidity uses EVM under the hood.](https://trustchain.medium.com/reversing-and-debugging-evm-smart-contracts-392fdadef32d)

### EVM 工具和习题

- 🧮 smlXL, ["evm.codes: Opcode reference and interactive playground."](https://www.evm.codes/)
- 🧮 smlXL, ["evm.storage: Interactive storage explorer."](https://www.evm.storage/)
- 🧮 Ethervm, [Low level reference for EVM opcodes](https://ethervm.io/)
- 🎥 Austin Griffith, ["ETH.BUILD."](https://www.youtube.com/watch?v=30pa790tIIA&list=PLJz1HruEnenCXH7KW7wBCEBnBLOVkiqIi)
- 💻 Franco Victorio, ["EVM puzzles."](https://github.com/fvictorio/evm-puzzles)
- 💻 Dalton Sweeney, ["More EVM puzzles."](https://github.com/daltyboy11/more-evm-puzzles)
- 💻 Zaryab Afser, ["Decipher EVM puzzles."](https://www.decipherclub.com/decipher-evm-puzzles-game/)

## EVM 实现

- 💻 Solidity: Brock Elmore, ["solvm: EVM implemented in solidity."](https://github.com/brockelmore/solvm)
- 💻 Go: [Geth](https://github.com/ethereum/go-ethereum)
- 💻 C++: [EVMONE](https://github.com/ethereum/evmone)
- 💻 Python: [py-evm](https://github.com/ethereum/py-evm)
- 💻 Rust: [revm](https://github.com/bluealloy/revm)
- 💻 Js/CSS: Riley, ["The Ethereum Virtual Machine."](https://github.com/jtriley-eth/the-ethereum-virtual-machine)

### EVM 支持的编程语言

- 🗄 [Solidity](https://soliditylang.org/)
- 🗄 [Huff](https://github.com/huff-language/)
- 🗄 [Vyper](https://docs.vyperlang.org/en/stable/)
- 🗄 [Fe](https://fe-lang.org/)
