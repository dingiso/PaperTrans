# 固件的托管，模拟和分析中的挑战

​	系统模拟和固件托管已经成为解决安全和性能相关问题的热门技术，例如当运行在特定的硬件平台时，固件是否存在安全隐患或是否满足时间需求。然而基于此的模拟和二进制分早以被探索和报道过，在此领域开展工作和研究是十分困难的。因此，我们为从业者和系统模拟的研究者提供了这个综合的指南。我们列出了在固件托管领域中常见的挑战，解释每一步并调研了在克服挑战的过程中使用的通用的工具，我们在五个维度进行分类，包括仿真器方法、系统类型、精度、仿真器目标和控制。这些分类和比较的标准使从业者能够选择最适合的模拟工具。我们使用该分类标准将行业内的热门工作进行归类，提出了在创造，模拟和分析一个系统时面临的28种常见的挑战，从获取固件到仿真后的分析。

## 1. 引言

​	连接设备的数量，从大型设备到智能家居和个人配件，随着物联网的爆发已经戏剧性的快速增长。大规模基础设施，例如输电网，蜂窝网络，以及其他的大型控制系统正随着日常工具一起变得智能化，数字化，互联化[62, 98, 107, 120, 178]。理解这些系统是如何工作的并发现固件漏洞成为了学界和工业界一项重要且不断发展的课题。很多大型企业愿意花费数百万美元发现他们设备上的漏洞。花费越来越多的努力保护这些互联设备是因为逐渐意识到他们的脆弱性，以及出现了更多的面向这些设备的攻击。这些系统掌握着敏感的金融数据，个人信息，控制着关键进程。恶意的攻击者不断的发掘漏洞去对商业，个人以及关键的系统，例如电网和蜂窝网络造成损害。

​	嵌入式系统的模拟是一项能加速发掘和缓解嵌入式系统固件漏洞的新兴技术。嵌入式系统的模拟传统上用于在开发中没有硬件环境也能编写和测试嵌入式软件。并发的开发昂贵难以获得大量的硬件或者容易损坏硬件的情况时，模拟是一个十分吸引人的选择。就像模拟能被用来在开发中验证系统行为，他也同样能被用来进行漏洞研究和分析。模拟提供了硬件所不具备的深入观察和测量固件的能力。相比于上层原码和二进制层的静态分析，模拟能帮助分析底层的操作，并且也是一种有用的工具，是第二节概述的许多漏洞发现技术的基础。

​	对一个使用模拟或托管固件的从业者，需要有一个学习曲线和大量可用的工具。我们最初的目的是给从业者提供一个固件托管的端到端的指导。我们专注于嵌入式领域的模拟和托管，因为实际上小到可穿戴物联网设备大到电厂，任何系统都将会有嵌入式设备，如果嵌入式设备出现漏洞将会影响整个系统的安全性和功能性。我们为从业者提供了一个当下技术和工具的汇总，并提供了分类和评估哪个工具最适合手头的工作的技术。我们还为从业者提供了一张关于哪个工具能解决哪些现有挑战的表用于后续章节的参考，给那些想要克服相应挑战的从业者评估正确的起点。

​	我们在第二小节中继续讲述一下仿真简短的历史和背景。在第三小节中介绍调研的工作，在第四小节介绍不同技术的比较。接下来我们在第五小节中比较用于仿真的所调研工作的不同。在第六小节中讨论必要的先决条件和固件托管的挑战。我们接着将挑战分为三个阶段，仿真前，仿真和仿真后并在第七八九小节中分别提供不同阶段工具的对比。最后我们以第十小节中说明选择托管工具时的考虑和第十一小节中的总结结束。

## 2. 仿真的背景

### 2.1 仿真的演变

从个人电脑时代之前，仿真就被用于拓宽硬件的使用范围并挺高模拟速度。例如，大部分打印软件都是为惠普设计的，所以很多不是惠普的打印机会写一个仿真器托管为HP设计的软件。重复使用惠普软件可以让新打印机更快地推向市场并减少开发新产品的时间和精力。

​	仿真理论最早是在1960年代早期发展起来的，为IBM System/360 系列电脑主机设计的 7070 仿真器是第一个实现的仿真器。这个仿真器允许IBM的客户在更新他们的硬件后仍能继续使用以前的应用。与7070仿真器的情况一样，早期使用仿真器是为了避免过时，并增加硬件与有限的可用软件的兼容性。随着时间的发展，制造商开始开发硬件模拟器用于在硬件制造前开发软件，以减少产品的开发时间。仿真器现在已经成为安全分析和逻辑调试的流行工具了。

​	在同一时间，模拟也同样被用于执行和扩展以超越现有的系统。在科学建模和研究中，有时会提到模拟，但是在此文的情景中，模拟是用于对系统内部建模的另一项技术。在计算机科学的语境中，模拟是通过实现内部来对系统建模而仿真是通过替换系统内部的某些模块来进行建模。通过替换内部的某些模块，仿真有时能降低复杂度或者提高固件托管的速度。仿真通常可以让原始的机器代码在仿真器上直接运行。若超出计算机科学的语境，仿真和模拟有时能互相替换，模拟有时也可以指用软件替换系统。在我们的实例中，这种区别是不重要的，但我们调研的工具大多将其技术称为仿真。

​	早期仿真技术的一个成功案例是90年代早期发表的Bochs。它模拟了PC操作系统开发所需的底层硬件，使操作系统与硬件完全隔离。这种隔离使得操作系统开发时只需要重启仿真器而不需要重新配置硬件。Bochs最初使用商业许可证但在2000年得以开源。除了Bochs，其他的仿真器也被开发出来，包括DOSBox，FX!32和PCem。他们主要用在x86和PC的仿真上。

​	随着多种仿真器的出现，执行和内存的精度(例如模拟的系统与真实的系统有多么的匹配，有时也指代准确性)从高(周期和寄存器精确)到低(模块和黑盒精确)。我们将在4.3节中讨论精度并给出更多的分类点，但是精度和仿真器速度可能是不同仿真器间最显著的区别

### 2.2 仿真基础

​	在21世纪初，Simics被创造出来并逐步发展成对多种架构进行仿真，包括Alpha， x86-64， IA-64， ARM， MIPS (32- and 64-bit)， MSP430， PowerPC (32- and 64-bit)， SPARC-V8 和 V9，和 x86 等指令集。它最初是由瑞典计算机科学研究所(SICS)开发的，后来转移到Virtutech，并最终来到了目前销售它的风河系统公司(Wind River Systems)。Simics被设计为指令精度，允许任何指令对间中断的测试。他还提供了配置管理，脚本运行，自动调试(前向和逆向)，以及内置了其他静态或动态分析工具中来构建一个仿真系统。Simics 的一个流行用途是在DARPA Cyber Grand Challenge期间自动审查提交的文件，以检查提交的二进制文件是否符合竞赛基础设施。

​	与在指令级精度下工作的Simics和其他仿真器相比，QEMU放弃了一些准确性来提高仿真速度。QEMU不是子指令模拟器(对每条指令执行多个操作)，它的执行和仿真是基础块级别的(一组顺序的，非控制流指令)，即将整个指令块翻译为目标系统的指令集并执行翻译后的指令。这使得 QEMU 工作的更快，因为它不需要在每条指令处检查中断，并且针对块的缓存大大减少了指令翻译的开销。因为其使用的开源许可证和良好的社区，QEMU 已成为学术界和行业专业人士使用的主要产品之一。它可以模拟IA‑32，x86，MIPS，SPARC，ARM，SH4，PowerPC，ETRAX CRIS，MicroBlaze 和 RISC‑V 架构，并为许多系统提供外设，使其和 Simics 成为使用最广泛的两款仿真器。

​	最新的可用的模拟器之一是Ghidra模拟器。Ghidra是美国国家安全局 (NSA) 开发的开源软件逆向工程工具。2019 年 3 月发布的最初版本包含了允许将传统软件逆向工程和仿真结合到同一 环境中的仿真工具。由于这款工具丰富的功能，⾃发布以来已经积累起了庞大的用户基础。Ghidra 使用他们⾃⼰的处理器建模语言叫作Sleigh和一种称为 P‑code 的寄存器传输中间语言，每条机器指令最多可转换为30条P‑code 指令。这意味着 Ghidra 仿真器工作在子指令精度(对每条机器指令执行多条仿真器指令)，以一个相对较高的执行精度为基础，Ghidra 目前支持各种架构，包括 X86 16/32/64，ARM/AARCH64，PowerPC 32/64，VLE，MIPS，MSP430，Z80，AVR等。在他们的框架中添加新的指令架构很简单，只需要用户指定如何将新架构指令拆成中间的P-Code语言即可。Ghidra 包括加载器，反汇编器，反编译器和分析工具，并提供以 Java 编写的基础分析器。除了内置仿真器之外，已经出现了Ghidra P-code模拟器，允许重新托管部分固件。

### 2.3 相关的漏洞发掘技术

​	由于仿真技术更频繁地用在漏洞发掘中，值得一提的是一些与漏洞发掘密切相关的技术也会影响仿真的发展。我们所介绍的技术也被集成到一些与基础模拟器一起工作的框架中，在讨论过程中⾄少需要对他们有一定的熟悉。我们不会深入研究这些领域，而是简要概述这些技术，并建议读者进一步阅读以进行深入调研。我们会提到一些更流行的工具，但在我们的评论范围内没有必要理解它们的差异或技术，而是在他们集成到工具时在后续部分中简要提及这些技术。

​	**符号执行**是利用符号来代表任意值作为程序的输入(类似代数中表示数字的字母)。符号执行的目标是分析程序以确定哪些输入可以让程序的不同部分执行。有别于分析和遵循具有具体值的单一路径，符号执行引擎将使用符号来描述可以通过使用符号约束的所有程序执行路径。

​	**混合执行**是指工具在仿真或执行期间可以在使用符号和具体值(如给代数符号设定值，例如 x=5)之间切换。在对固件逆向工程时，分析者偶尔会想确认在什么条件下程序将执行特定部分的代码。符号和混合执行是在其他漏洞发掘用途中帮助解决此问题的工具。符号执行现在是一种常见的软件测试实践，尽管它早在 70 年代就被引入了。常用的符号和混合执行工具包括[10， 13， 15， 16， 19， 23， 24， 28， 38， 47， 61， 79， 103， 111， 141， 144， 147， 151]。


​	**模糊测试**是一种通过提供随机输入进行⾃动漏洞/故障发掘的技术，并观测系统是否存在意外的行为(例如，崩溃)。模糊测试面临许多挑战，各种工具都试图解决这些挑战。在仿真的语境中，模糊器通常利用对⼆进制⽂件执行的可见性和控制权来优化他们的随机输入，以改进他们对⼆进制⽂件的探索。可以与仿真集成的流行的模糊测试工具是 [2，14，39，51， 63‒71， 80， 108， 127， 134， 135， 145， 156‒158， 184]。

### 3. 调研过的工作

​	在介绍仿真比较的维度之前，我们介绍一下支持仿真和固件托管的调研工作。我们不会讨论或重新介绍第 2.2 节已经介绍过的 Simics，QEMU 或 Ghidra 等仿真器，但它们是本节介绍的工具中使用的基础仿真器。基础仿真器可以进行用户模式仿真(即仅运行用户级应用程序)或全系统仿真；全系统仿真目前是固件托管的主要模式。全系统仿真将仿真处理器和外设；但是与嵌入式系统中硬件的多样性相比，基础仿真器中可用的仿真外设集是很小的。固件仿真中的大部分工作旨在解决仿真外设的不⾜。


​	**Avatar^2^** 是一种专注于固件分析的动态多目标组合和检测框架。该工具是和 Avatar 相同的团队创建的，但是是一个多目标组合工具，对最初的Avatar实现进行了重新设计。

​	该工具的主要贡献在它允许和各种其他工具(angr，QEMU，gdb)进行交互和传输数据。使用这   些工具，可以在仿真循环中使用硬件，其中部分执行或内存访问交由物理硬件执行。

​	**angr**是一个符号执行引擎，已与Avatar2 集成来将符号执行和其他分析结合到QEMU仿真器中。angr 是一个⼆进制分析框架，为许多静态和动态分析提供构建块。angr 提供一个架构库，一个可执行⽂件和库加载器，一个符号执行引擎，内置了分析工具，和一个环绕二进制码转换器的python包装器。它积极开发并被使用在其他各种学术著作，包括[9，104，124，128，148，150，158，175，176]。我们将其列为调研的工作之一是因为它既能够使用基础模拟器，同时也能提供新的⽅法和解决⽅案来克服从仿真开始到结束的挑战。

​	**HALucinator** 通过观察通常由硬件抽象层 (HAL)执行的与外设的交互来解决有些外设基础模拟器没有提供的挑战。它使用Avatar2和QEMU作为基础来拦截 HAL 调用并替换它们。他们通过手动编写在托管期间执行的 HAL 函数来进行替换。它使用库匹配工具来识别固件中的 HAL功能。该工具依赖于假设大多数固件程序员在编写固件时使用HAL，根据我们的经验，这是一个相对安全的假设。

​	**PANDA** 是一个开源平台，构建在QEMU全系统仿真器之上，用于与架构无关的动态分析。PANDA 的主要优势在添加了记录和重放执行的能力，允许进行深入的全系统分析。系统记录已经部分得到解决通过硬件‑软件协同设计⽅法[161]以及在更严格的假设下的纯软件⽅法[159,162]。这种全系统的记录和回放是具有挑战性的，尤其是考虑到时间要求。PANDA使用 QEMU 作为基础并对分析进行抽象，允许在多种计算机架构上使用单一分析的实现，同时保持QEMU的速度，来解决时间上的挑战。

​	**Muench2018**，题为“内存损坏不是崩溃：针对嵌入式设备的模糊测试中的挑战” 的文章中证明了用在桌面设备漏洞发掘和测试的工具不一定会适用在嵌入式设备中。在他们的论⽂中，他们展示了通过检测用于固件托管的模拟器来进行漏洞评估的不同技术。他们通过结合PANDA 和 Avatar2实现了段跟踪，格式说明符跟踪，堆对象跟踪，调用栈跟踪，调用帧跟踪和栈对象跟踪。

​	使用 QEMU 作为基础，**Firmadyne** [25]和 Costin 固件分析(以下称为**CostinFA**  “嵌入式固件全性的大规模分析”和“大规模⾃动动态固件分析：嵌入式网络接口案例研究”)，将从给定的固件中提取⽂件系统，并将⽂件系统重新托管在他们⾃⼰的内核上。他们仅使用软件执行系统仿真，不涉及任何物理嵌入式设备。他们的工具仅适用于可以原生使用 chroot 和 QEMU 的软件镜像。之后他们对其重新托管的固件执行静态和动态分析来报告漏洞。最近使用的 Costin 和 Firmadyne 相同类型的⽅法是ARMX。ARMX 需要更多的用户交互和配置，并且仅适用于ARM 架构设备，同时还需要固件中的 rootfs 和固件中提取的 NVRAM。因为这些额外的要求，使用他们的技术来仿真设备的结果通常是高质量的。


​	**PROSPECT**和**SURROGATES** 通过将硬件和外设访问转发到实际设备来进行仿真；一种称为硬件加入循环的技术。PROSPECT 通过与设备的正常总线连接转发外设和硬件交互，但允许无需了解连接到系统的外设和外部硬件的详细信息时进行分析和实现。相反，SURROGATES 在主机 PCI Express 总线和测试系统之间使用特质的低延迟 FPGA 桥接器，允许在系统和外设之间进行转发和状态传输，传输速度比原始 Avatar [185] 系统快得多。

​	**P^2^IM**使用插入式模糊测试器 (AFL)为基础 QEMU 仿真器提供输入。它抽象外设和硬件IO，然后使用模糊测试器给基础模拟器提供反馈。他们的⽅法不同于现有的仿真⽅法，因为是模糊测试器负责交互所以不需要硬件参与也不需要外设的详细知识。因为模糊测试器可以通过简单的外设来模拟执行固件，但是是否能够模拟处理来自复杂和有状态的硬件的固件的数据的能力是未知的。

​	与使用随机模糊测试器相比，**Pretender**尝试通过使用机器学习得到硬件交互模型来重新托管固件。他们的系统将记录硬件交互以及对内存映射区输入和输出的所有访问(与外设的交互是通过特定的内存映射地址 ‑ MMIO完成的)以及在执行外设集群前执行阶段发生的所有中断并将记录划分为对应每个外设的子记录。然后他们训练了一个模型，尝试为每个外设选择或在已知模型上进行训练。最终由分析人员决定如何将输入引入系统。

​	总结所有调研的工具(在第 2 节和第 3 节中以粗体突出显示的那些)，具有广泛特征和用途的通用仿真器包括Avatar2，CostinFA，Firmadyne，Ghidra，HALucinator，Muench2018，Panda QEMU 和Simics。在仿真循环中使用硬件的仿真器包括Avatar2，SURROGATES和 PROSPECT。可以使用符号执行和/或模糊测试的仿真器包括 angr Ghidra HALucinator 和P2 IM。唯一使用机器学习训练模型的仿真器是 Pretender，目前它需要对相当简单的固件进行仿真才能成功。如果读者急于想得到一个流程图来判断该使用哪种工具，请参阅第10节中的图4。虽然这不是对仿真工具或⽅法的详尽列表，因为还有很多可用的工具或⽅法，包括[17，18 ， 33， 40， 48， 48， 60， 75， 76， 82， 86， 87， 92， 94， 99， 136， 139， 155， 156， 164， 167， 173]等等，我们相信这些工具的范围和它的适用性已经包含在上述工具中。

## 4. 仿真比较的维度

​	在讨论仿真的核心挑战之前，我们首先介绍仿真器比较中的各种维度。多种多样仿真器和仿真技术有不同的目的，因此有着不同的设计判断。建⽴一些通用的维度来评估仿真器对从业者针对不同的使用场景选择理想的仿真工具是十分必要的

### 4.1 仿真技术

​	重新托管固件的一个重大挑战是需要仿真大量的硬件外设。因此，其中一个评估维度是仿真器用来提供这些外设的基础技术。仿真器所采用的技术将直接与另一个比较维度相关—可以模拟的硬件的复杂性。一些系统很简单，没有外设，而其他系统可能连接到远程终端单元 (RTU) 可编程逻辑控制器 (PLC)，现场可编程门阵列 (FPGA)，多传感器，多数据库，人机界面控制器 (HMI)等。从业者想要模拟的硬件数量范围从单一芯片或传感器，一直到整个大型系统。硬件仿真中硬件数量和复杂性在很大程度上受到所使用的外设仿真技术的限制

​	主要外设交互仿真技术包括硬件纳入循环(HITL)，机器学习，模糊测试和抽象替换。**HITL**将使用仿真器进行指令执行，但如果访问硬件外设，则将消息转发给实际的物理硬件，机器学习是指使用机器学习来提供硬件交互，而模糊测试将使用随机生成技术来提供模拟硬件的交互。抽象替换通过识别固件中的软件抽象来提供外设硬件的功能，并用⾃⼰的实现来替换这些抽象的执行。使用HITL的示例是 SURROGATES[90]和 Avatar2 [112]。P^2^IM[57]使用模糊测试，Pretender[72]使用机器学习，而 Firmadyne [25]，CostinFA[43， 44] 和HALucinator[35]使用抽象替换

### 4.2 系统类型

​	除了硬件外设的仿真⽅式之外，考虑仿真器设计所支持的系统类型也是很重要的。嵌入式系统的范围和能力可以从运行特制版本的桌面操作系统(例如，Linux)的大型多处理器系统到运行几KB代码而无需操作系统的低成本，低功耗的微控制器。模拟这些系统的挑战和技术各不相同，可能会也可能不会在不同系统互相通用。我们重用Muench等人提出的分类方法，虽然使用我们⾃⼰的名称而不是系统类型的数字，但根据它们执行的固件类型，将嵌入式系统类型分为三个不同的种类

​	**通用嵌入式系统** (GPES)： 也被称为第一类嵌入式系统，使用主要用于服务器和桌面系统的通用操作系统。例如实时 Linux 操作系统，嵌入式 Windows 和 Raspberry Pi。这些系统针对嵌入式环境进行了改造，但仍保留了许多桌面级功能，只是组件进行了精简，并与轻量级用户环境相结合，例如busybox或uClibc。像 Firmadyne 和 CostinFA 这样的工具要求他们使用的嵌入式系统是基于Linux的系统，并且只能在这种类型的系统上运行。仿真这类型的系统极大地受到为模拟桌面软件和操作系统所做的工作的影响(例如， QEMU 直接支持模拟Linux 内核)

​	**专用嵌入式系统** (SPES)：(来⾃[113]的第二种类型设备)使用专门为嵌入式系统开发的操作系统。它们通常是商业产品和封闭源代码。例如µClinux，ZephyrOS 和 VxWorks。这些系统通常是单一用途的电子或控制系统。这些系统有区别的一大特点是操作系统和应用程序可以单独编译，并且系统不是从桌面操作系统派生的。因此，许多桌面系统的仿真技术都不起作用，仿真必须从头开始。重新托管这些系统需要重新托管内核和用户空间。仿真该系统的另一大挑战是内核和用户空间之间的分界线通常是模糊的。

​	**裸机嵌入式系统** (BMES)：第三种类型的设备是没有真正的操作系统抽象的嵌入式系统，我们将其称为裸机嵌入式系统 (BMES)。它们通常没有操作系统，或可能只包含轻量级操作系统库。 例如 Arduino 系统。在这两种情况下，应用程序将直接访问硬件和操作系统(如果存在)，并且应用程序会静态链接到单一的⼆进制⽂件中。最近的一些工作 [35，57，72] 侧重于重新托管这种系统。

​	我们发现这些比较的维度很有用，因为它有助于确定分析者应该为给定的固件考虑哪些仿真技术。然而在实践中，对系统进行分类并不是一成不变的。相反，分类是嵌入式系统仿真的连续统一体。例如，一个30年前就开始使用 UNIX 的嵌入式系统最初可能被归类为 GPES，但系统随着时间的推移而发生变化，同一个系统现在可能更适合归类为 SPES。



### 4.3 精度

​	在第 2 节中介绍过，精度可能是最重要的比较的维度，但也是最难被量化的。其中的难点来⾃于检查硬件内部状态的能力有限，并且由于缺乏比较状态的能力而变得更加复杂。为了更好地理解精度，我们沿着执行精度和数据或内存精度的概念的维度对精度进行分类。这将对精度的概念的比较限制在2D平面上。Costin [44]的工作对具有内核和应用程序的仿真器进行了一种普遍的分类。我们这里介绍的分类更笼统，适用于重新托管和仿真器，分类点更多，且适用于所有类型的系统——GPES，SPES 和 BMES。精度的分类是从固件(软件)的⻆度，以及仿真是否“看起来”和“动起来”像真实的硬件。这意味着我们不需要区分与固件相似的内存种类(是不是DRAM SRAM Flash 等)，而只需要把他们都当成内部存储。如果有其他的驱动程序需要使用特定的存储结构(例如 SD卡)，我们就把他们当成外置存储器。

​	执行精度描述了仿真器中的执行与实际物理系统的匹配程度。我们将技术分类为*黑盒，模块，功能，基本块，指令，周期和完美*，执行精度从黑盒递增到完美。使用黑盒精度模拟的系统展示出与真实系统相同(或⾜够相似)的外部行为，但在内部可能会也可能不会与真实系统执行相同的指令。模块精度提供了模块级别的精度。例如，Firmadyne 用自己的内核替换原始固件的内核来实现重新托管原始固件。因此，固件的某些模块未经修改即可执行，而其他模块则被全部替换。功能级别精度准确地在功能级别对系统建模(例如，HALucinator 替换整个功能来实现仿真)。类似地，基本块和指令级精度在基本块和指令级层准确的进行仿真。在指令级别之上是周期级别，忠实地仿真到 CPU 指令周期(例如，gem5模拟器)。完美的仿真意味着仿真与实际硬件完全相同，据我们所知，当前的所有仿真器都无法达到这一点。

​	我们将数据/内存精度从最粗粒度到最细粒度分类为*黑盒，内存，寄存器和完美*。黑盒精度意味着系统和外部存储器(例如，HDD 或 SDD)从外部看见的数据是相同的(例如，对给定的输入，我们得到相同的输出)。 内存级别意味着内部存储器(例如，RAM)与硬件在给定的执行时间点上是一致的。寄存器级精度意味着内部存储器和寄存器在给定的执行精度下都是正确的而完美意味着所有内存组件在所需的执行精度级别上与给定系统的工作⽅式完全相同。在黑盒，内部存储器和寄存器级别中，这种分类通常对固件中某个特定的领域感兴趣。这意味着分类点之间存在模糊，因为当用户不太关心内部存储固件的某些部分可能处于黑盒级别，但在固件里一些高度关注的部分，固件仿真可能处内存级或寄存器精度。完美仿真级别需要有一定的规模，但目前几乎无法达到。根据从业者的目标不同，完美仿真可能意味着整个固件都能达到寄存器级别，或者可能意味着一切都是准确的，细到每个执行周期的缓存。在图 1 中，我们展示了最流行的固件仿真技术如何匹配到这一分类框架中的。

### 4.4 仿真器的目的

​	在所调研的固件重新托管的研究工作中，主要关注的点是*创建仿真器，动态分析，静态分析和模糊测试*。每一个关注的点都让模拟器能够回答具体的问题。仿真器的用途包括漏洞检测，运行遗留代码，更换硬件，协助开发和系统行为分析。仿真器的目标直接影响所采用的技术，仿真器将在其上工作和为之工作的系统类型，和系统的精度。

### 4.5 控制水平

​	与仿真器的目标相关的另一个比较维度是控制固件探索的能力以及可以/应该使用什么工具。控制可能被认为是精度的另一种维度，衡量你是否可以控制仿真以及仿真过程中实际执行的内容。还指从业者可用的交互级别。例如，HALucinator 支持交互式仿真，使其适用于构建虚拟测试平台，而P^2^IM 支持模糊测试，不是可用于测试平台的工具。

​	为了方便可视化，我们没有将精度和探索结合起来，但重要的是要注意有些工具和模拟器不允许对探索进行控制。例如，如果你的模拟器只是对从内存到输入的所有数据进行模糊测试， 可能具有⾮常高的执行精度和低的内存精度，但是几乎不能指定要执行的内容来探索， 而仅仅依赖随机生成器。随机化和模糊测试可以做到高覆盖率的模糊测试和漏洞发掘，但是并不能清楚地显示实际执行的可能性。

## 5 调研的工作的分类

​	我们已经讨论过了精度的不同维度，现在进一步对用于固件重新托管和仿真的调研工作进行分类和比较。图一展示了根据精度分类的二维平面。横轴是执行精度，纵轴是数据精度。从图中可以看出，每个类别都没有精确的点，而是一个模糊的⽓泡，强调精度分类是概念性的，是从黑盒到完美的连续统一体。如图一所示，绘图上的颜色范围从深到浅。 圆圈越深表示工具自动化程度越高，需要用户交互和配置的越少。图中的圆圈越亮表示工具默认状态下需要更多的用户交互去设置。我们故意不对⾃动化或精度级别给出具体数字，而是在视觉上对⾃动化进行分类并将精度放在一个区域中以再次强调这些分类并不具体，并且可能会根据工具使用和从业者的意见而略有移动/变化。

### 5.1 硬件加入循环

​	随着各种工作对核心仿真器的修改，精度可以提高或降低。当精度提高时，通常会在性能或复杂性上进行权衡。SURROGATES 使用 QEMU 作为基础仿真器，但添加了专门的硬件来加速与真实硬件的通讯。专用硬件有更高的执行精度，因为外设访问是完美的。HITL 还提高了数据精度，因为没有像 PROSPECT 那样对外设模型进行泛化。然而专用硬件和 HITL 通常会增加执行仿真的复杂性和成本，同时降低可扩展性，因为每个仿真系统都需要有专门的硬件。

### 5.2 指令级执行精度

​	我们将 Simics 和 Ghidra 仿真器归类为指令级执行精度，同时由与他们利用了子指令执行而具有寄存器级数据精度。虽然⾃动化的程度是所有调研的作品中最弱的，但这两种模拟器都有现成的实现，可以复制或开箱即用，使自动化更接近无需用户交互的水平。然而，由于工具的默认设置，我们把他的自动化归为此类，以展示使用这类模拟器的开放性。

### 5.3 基础块级执行精度

​	如前文所述，QEMU默认具有基本块级的执行精度和 RAM 级数据精度。这些都是默认情况下的分类，它们可能会受到其他工具的影响。Muench2018，angr，Panda 和 HALucinator 使用 QEMU 作为基础仿真器没有使用HITL，因此总体上精度会小于或等于QEMU。

​	对于任何内存交互， P^2^IM 都会进行随机模糊测试，这意味着数据维度没有达到黑盒精度。Pretender使用机器学习来尝试提供外设软件的替换模块，降低数据精度，但在数据维度上仍然有一定内存正确性并且至少是黑盒精度。由于这些作品降低了精度(在任一轴上)它们同样降低了仿真所需的工作量。Muench2018 和 PANDA 执行第三节中提到的跟踪/记录。在允许重放和复制时，由于QEMU基础仿真器的精度限制，精度仍然达到了基础块和内部存储器/寄存器精度的最大值。

​	angr 还在仿真部分使用 QEMU，同时也使用符号执行。符号执行很难放在仿真精度的网格上，因为它在执行时具有多种状态，但是通过使用 QEMU 进行混合和具体执行，angr 获得与 QEMU 相同的精度。相比之下，Avatar^2^支持几乎任何工具之间的交互 ，允许精度在任何工具精度分类，具体取决于分析师如何编程 ，但我们将其默认精度展示在 QEMU，angr，PANDA 和HALucinator 周围因为它们都是能很容易的在 Avatar2 框架中运行。

### 5.4 模块级执行精度 

​	Firmadyne 和 CostinFA 都失去了基础 QEMU 仿真器的执行和数据精度，因为它们用⾃⼰的内核提取⽂件系统并运行代码。然而，通过这样方式，他们大大提高了工具的⾃动化程度，使固件的大规模分析更具可扩展性。

## 6. 问题和挑战

​	本⽂的其余部分将重点关注您，即想要重新托管固件的分析师，并讨论会遇到的挑战，以及可用于解决这些挑战的技术和工具。所调研的挑战和工具为普通从业者提供了参考，或为固件重新托管和系统仿真领域的新研究人员提供了起点。

### 6.1 问题的目的和价值

​	每个仿真工具都必须满⾜一定的要求，才能重新托管固件。一些工具可以用技术绕过常见的仿真挑战。理想情况下，该工具将⾃动克服挑战，否则从业者必须手动来完成。第七节讨论一些常见的先决条件，但是这些讨论包含所面临的挑战的内容，而不包含遇到这些挑战的原因

​	在决定仿真系统或重新托管固件之前，分析师有一个他们想要回答的问题。为什么要对系统模拟是构建模拟器的关键，且论⽂中通常不会特殊强调。当分析师想要发掘漏洞时，在每个精度级别上可能都有想要发掘的漏洞。因此，你要找的漏洞的类型是⾮常重要的，例如， 它是不是是只要通过正确模拟内存就可以检测到的固件逻辑错误，还是需要更高执行精度的指令或硬件错误？

​	在模拟系统之前，对使模拟正常工作的成本和收益价值进行评估以及分析执行模拟的工具是很重要的。为了模拟某一系统，可能需要小型工程师团队工作 6个月到一年的时间来构建和测试所需的系统。这可能很划算，也可能代价过高。分析可用的工具以及了解如何使用它们可以加速甚⾄改变构建模拟器的传统⽅法(手动对硬件进行逆向工程，重新托管固件直到出故障并逐步添加功能)。

### 6.2 关键研究问题

​	理想情况是只研究一个特定领域，但是对当前有的任何一种解决⽅案/工具，都存在不⾜之处。 要理解什么是研究的关键，就一定要知道你希望通过仿真解决什么问题。我们看到需要   解决的关键领域是缩短模拟一个系统的时间——这个挑战需要后面所有章节的工具来克服。为一个工具编写的脚本可能会解决一个特定的挑战，但如果使用另一种工具则需要移植该脚本，例如，用于 Ghidra 的帮助找到入口点的脚本不一定适用于 angr 或 Simics。

​	这个加速仿真的关键研究领域涵盖了所有类型的嵌入式系统的研究(GPES SPES 和 BMES)，并包括能克服第七节，第八节和第九节中提出的问题的新⽅法。任何应对挑战的更有效的工具或技术都是有用的研究领域。

### 6.3 挑战

​	既然我们已经介绍了调研的工作和分类标准，我们将介绍固件重新托管和系统仿真期间面临的核心挑战。在仿真过程中，会遇到各种挑战，我们将这些挑战分为：

* 仿真前 
* 仿真中
* 仿真后

​	仿真前是仿真执行的先决条件，克服这些挑战让仿真器可以执行第一条指令。这些挑战包括获取固件，解包固件以及获取和了解如何配置仿真器以重新托管固件。一旦在仿真器中执行第一条指令，我们就认为仿真阶段已经开始。但是，随着执行的进行，对固件的理解会更加深入，这会导致仿真器配置和实现的改进。因此，我们将仿真分为配置仿真器-能够进一步优化仿真器和更完善执行的挑战-和运行仿真器-对仿真本⾝⾄关重要的挑战。最后在仿真之后是仿真后阶段，在该阶段执行分析和验证。此处提出的挑战已被部分提及并用作多项工业和学术项目的基础，但为了完整起见，我们在此将其全部呈现在本文中。

​	在本⽂其余部分的表格中，我们调研试图解决仿真和⼆进制分析过程中面临的各种挑战的不同的技术。如果有选择标记，则该工具尝试解决了问题；但是， 不一定能解决问题，并且有   些工具比其他工具效果更好，尽管没有具体说明我们的看法。如果有破折号，则意味着该工具通过他们采用的技术绕过了挑战。如果没有标记或和折号，则该工具无法解决挑战。在某些情况下，这些工具正常工作需要有先决条件。模拟器可能需要分析师/用户手动找出挑战并将在模拟器实现解决⽅案以克服挑战。

​	我们在图二和图三中提供了在仿真过程中面临的一些流程和常见挑战，但并未涵盖可能面临的所有挑战，为简洁起见省略了一些挑战。这两个图像是常见流程，每个⽓泡代表整个过程中遇到的一个或多个挑战。第 7 节，第 8 节和第 9 节进一步详细讨论不同的挑战。

## 7 仿真前

​	在图二中，我们展示了从业者通常会在重新托管固件的仿真前阶段探索的流程。每一步都说明在继续到下一个流程之前需要解决的挑战。这里提出的挑战和 Costin[43] 提出的挑战类似。在这里，我们专注于模拟和分析单一固件的挑战，而 Costin 专注于获取和分析可从网络⾃动下载的成千上万的固件的挑战。

​	在重新托管固件之前，分析师或从业者通常会有他们想要模拟的系统或他们想要重新托管的固件。在某些情况下，系统架构是未知的，或者分析师甚⾄可能没有固件(例如，在处理漏洞悬赏或专有硬件时)。即使在获得系统或固件之后，也必须在重新托管固件之前确定一些关键信息。设置模拟器之前的步骤和挑战就是我们所说的仿真前阶段。这些阶段还可能包括在实际仿真之前获得的信息的理解和验证(即反汇编，初始分析和CFG恢复)，尽管验证并不一定严格需要。我们注意到，当后续阶段遇到挑战时，正确性验证很有用(可以包括形式验证和/或行为验证)，因为它缩小了具体问题的根源的范围。


​	开始仿真所需的信息因仿真技术而异，但包括获取固件，确定内存布局，确定指令集架构 (ISA) ，识别处理器，分析⼆进制⽂件，固件的转换/反汇编和初始固件分析。作为对从业者的   快速参考，表一总结了尝试解决仿真前中存在的挑战的不同技术。

​	用于解决这些挑战的主要工具之一是 binwalk [139]。Binwalk 主要用从固件映像中提取内容，但还有其他有用的功能。可以确认ISA(不一定精确到处理器)，从二进制数据块中提取⽂件，进行字符串搜索，查找签名例如常见加密格式，使用 capstone[22] 进行反汇编，计算熵，并执行⼆进制差异比较。其他一些工具将在后续小节中提及。BlackHat 2013[41] 提出了一些从二进制数据块进行逆向工程的简单示例，其中提到后文中的一些挑战。他们展示固件的端到端解包，其中的一些格式可能会对该领域的新手有所帮助。OWASP 维护了一些基于OpenWrt 的   物联网固件可供参考。他们提供了一个有关物联网设备漏洞的平台来教导软件开发人员和安全专业人员[121]。

### 7.1 获取固件

​	重新托管固件的第一个挑战是获取固件。在最简单的情况下， 可以从供应商的网站下载。如果不能直接从供应商处获得， Github 等三⽅网站也提供了已用在学术研究的固件。获取固件的其他⽅法包括从开发板获取示例固件，这些固件可以使用多种操作系统和工具链进行编译。

​	下载固件并不总是可行的，即使可行，固件也可能嵌入不易提取的专有⽂件格式。 例如，文件可能被压缩或固件包含多种架构⽂件，并在引导加载期间在硬件上完成固件的最终编译。在某些情况下，固件与操作系统相结合，例如 BMES 或 SPES 类型的系统，而其他一些GPES系统中能下载的固件也只是用户级应用程序，还必须单独获取操作系统内核才能执行全系统仿真。为了克服嵌入式解包问题，有时可以使用连接到实际硬件的网络捕获工具(例如[31，53，55，88，106，116，117，125，130，163，165，179])来捕获固件更新期间的网络流量。然后从捕获的数据包有效负载中提取固件。
​	除了下载之外，有时还可以从硬件中提取固件。⽡西尔等人[170]在他们对基于硬件的固件提取技术的调研中显示，很大比例的系统暴露了⾜以获得固件的 UART 接口。除了UART 端口，调试端口(例如，JTAG)和 USB 端口也可用转储固件[56，186]。在某些设备上，可以在对引导加载程序中的固件更新协议进行逆向工程后使用闪存读取命令来物理采集固件[182]。如果这   端口加锁并保护了，另一种选择是从电路板上移除内存并连接到另一个系统以转储固件。从印刷电路板上移除内存会有相当大的损伤或破坏硬件的⻛险。使用调试端口需要调试端口存在并且未上锁，这两者都越来越被认为是糟糕的安全设计。


​	在我们调研的工具中，Firmadyne 和 CostinFA 从供应商网站获得固件，而P^2^IM，angr 和 Pretender使用从第三⽅供应商 (Github) 获得的固件。HALucinator 和 Pretender 还在评估中使用来⾃真实嵌入式板的开发示例。对于Simics，QEMU 和 Ghidra，他们是基础仿真器工具，而不是学术论⽂，因此他们没有指定如何获取固件，而是希望用户在使用该工具之前拥有固件。其他调研的工作中没有具体说明他们的固件样本是如何获得的，只是说明了固件是什么，或者他们模拟的系统是什么。

​	Firmadyne 的作者开发并发布一款从嵌入式系统供应商网站下载他们可以识别的任何固件的爬虫。然后他们以通用⽅式解包固件，如果固件解包正确，Firmadyne 将继续仿真，否则会崩溃。在 Firmadyne 抓取的[23，35]固件中，他们提取了其中的 9486个。在提取的固件中，有 1971个在简单的仿真中成功。大比例的失败 (90%) 表明获取和提取固件是一个真正的挑战，并且在许多情况下难以克服。CostinFA 公开发布的 URL 中尝试解包和分析固件[42]。CostinFA 收集了从可公开访问的固件更新站抓取的759273个初始⽂件集，并将其过滤到 172751个可能可用的固件映像。然后分析了一组 32356个固件的例子，发现了38个漏洞[43]，尽管尚不清楚所涉及的仿真或重新托管的级别。其他可能有助于获取固件的资源包括 Python 抓取工具和其他开源存储库 [59，131，142，172]。

### 7.2 指令集架构

​	获得固件后，需要确定固件使用什么指令集架构(ISA)，以便仿真器可以将固件反汇编成正确的机器指令。字节序(例如，小端序或大端序)和机器字大小。除了确定 ISA 系列(例如 ARM PowerPC X86 MIPS ARM64 AVR 等)之外，有时还需要知道 ISA 版本才能正确反汇编指令，例如，该ARM是否支持Thumb子集和浮点指令？ISA 通常可以从要仿真的处理器的数据表中确定。如果硬件未知且无法识别数据表，则可以使用静态分析技术。这些技术首先尝试使用文件应用确定⽂件格式是否为已知⽂件格式(例如，ELF，PE2 Mach‑O)，然后查看固件中的其他签名(例如加密，压缩等)，或分析⼆进制中的字符串以猜测 ISA。一种可能有助于确定ISA 的众所周知的工具是 binwalk [139]。Binwalk 将使用 capstone 反汇编程序并尝试为各种类型的 ISA 反汇编⼆进制⽂件。如果给定架构的一行中有超过指定数量的指令(默认为 500 条)，那么这是 ISA 的有力候选。在调研的工具中，Firmadyne 和 CostinFA 使用 binwalk 和现有的提取工具来确定ISA，然后提取⽂件系统，或先提取⽂件系统后确定ISA—这两个步骤的顺序和手头的固件和分析过程高度相关。Ghidra 有无头的脚本，运行它们就可以试验和确认ISA(包括使用 binwalk 的仿真)，而 angr 提供了 boyscout [5] 工具来尝试确定固件的架构和字节序。其他调研的工具希望给定了 ISA 才能够正常工作。

​	最近还有使用机器学习对 ISA 和字节序进行分类的研究。这些技术通常依赖于进行⼆进制相似性检测。他们将使用已知的架构来训练⼆进制⽂件，将⼆进制⽂件分成更小的可比较片段，并将它们转换为向量，以便于使用机器学习和随机梯度下降优化。Clemens 等人的一个这种的工作[34]对来⾃20个不同架构的 16785个不同固件进行试验，并且在99%的时间内准确地对 17个架构进行分类，其余 3个架构(mips mipsel cuda)的分类准确率为50% 。德尼古拉等人[49]利用有监督的顺序机器学习技术来定位⼆进制⽂件的代码段边界，以帮助减轻使用类似大小的数据库进行机器学习的分析难度。Kairajärvi等人[81]综合了Clemens [34]和 Nicolao [49]的想法并进行了改进，通过使用和公开发布一个更大的更平衡的数据库用机器学习和测试。[181]中提供了其他可以更改为用于确定ISA的机器学习技术和一些用于二进制分析的机器学习技术。这些技术需要大量数据集来进行准确的训练，并且受限于它们的训练数据。

​	如果上述⾃动化⽅法失败，从业者可以尝试通过查看固件中的签名和字符串(某种压缩，签名 版权等)来手动识别 ISA和/或暴力反编译并执行“眼睛”测试那些看起来很有希望的东西。

### 7.3 确定基地址

​	为了使固件在仿真器中执行，它必须加载到正确的地址。如果重新托管的固件是⼆进制块(例如，只是一个没有符号或元数据的⼆进制⽂件)那么确定基地址是很困难的。有时可以通过硬件数据找到应该加载固件的基地址(对于从内部存储器执行的固件)。如果源代码和编译工具可用，则可以在链接描述⽂件中找到基地址信息。

​	查找基地址是许多⼆进制分析技术的基础，因此(尝试)⾃动确定基地址的技术已被广泛研究。朱等人使用字符串和 LDR 指令，比较它们并匹配偏移量以确定固件映像的基址[187， 188]。这类似于将⽂件中出现的双字与⽂件中的字符串一起列出并尽可能排列出他们之间的距离的技术。如果相匹配，则减去偏移量将为您提供基址。

​	Firmalice [150]通过分析跳转表的位置和间接跳转指令的内存访问模式来利用⼆进制⽂件中的跳转表。在跳转表中有一组绝对代码地址，可以更好地确定固件需要位于什么绝对地址才能正常工作。为了找到跳转表，他们在⼆进制⽂件中扫描有效的最少两个字节中不同的连续值。在许多情况下成功找到了跳转表，因为跳转表通常连续存储在内存中。找到跳转表后，他们然后分析在反汇编阶段发现的所有间接跳转以及他们从中读取跳转目标的内存位置。然后重新定位⼆进制⽂件，使得最多的数据访问与跳转表相关联。

​	angr 还尝试使用 girlscout [6] 分析脚本来确定基址。该脚本将尝试通过查看函数并进行统计分析对最可能的基地址进行投票来决定基地址。如果⾃动化技术不起作用，则可以通过蛮力猜测和检查来发现基地址，因为大多数基地址是 2 的幂的倍数

​	在Firmadyne 和 CostinFA中，在确定 ISA 后就是从固件中提取⽂件系统，然后在提供给 QEMU 的⾃定义内核中使用。通过使用他们⾃⼰的内核，这些工具绕过了确定基地址的需要，但也降低了执行精度，因为它不执行原始内核。对于其他调研的工具，都假设基址由从业者提供的。

### 7.4 找到入口

​	在确定固件的基地址之后，从业者需要确定入口点(即从哪里开始执行的地址)。入口信息可以在⼆进制中编码(例如，可执行链接器格式，ELF 在元数据中定义入口)，或者可以运行不同的分析来帮助从业者提供入口点选项。

​	对于随机⼆进制块的固件，angr，Ghidra 和IDA[136]有一些脚本，通过扫描⼆进制⽂件试图找到函数序言指令和函数的返回。从函数信息可以分析和生成一个有向函数调用图。调用图中弱连接组件的任何根节点都可以视为潜在的入口点。函数调用图的创建和分析要求您已经知道ISA(因此才可以分析函数序言，函数尾声和调用指令) 。调用图技术通常会返回多个入口点，必须从中识别出正确的仿真入口点。此外，固件通常会有多个有效的入口点(例如，引导加载程序和中断服务程序)

​	有些技术不是通过查看固件来识别入口点，而是依赖它们支持的硬件信息来确定入口点。例如，HALucinator 针对 ARM Cortex‑M 设备。Cortex‑M 架构定义初始程序计数器的值在复位时存储在地址0x4。因此，HALucinator 通过查看地址 0x4 来找到入口点。如果硬件已知，数据集可能会提供有关系统如何开始执行的信息。

​	对于使用替换内核的技术，如 CostinFA 和 Firmadyne 的情况，从根本上绕过了寻找入口点的挑战，因为内核的入口是已知的，因为他们⾃⼰构建了内核。如果从业者正在处理已知的操作系统或编译器工具链，则可以指定入口点函数名称(例如 _start 或 _init)。然后有一些技术可以进行功能匹配，为您提供实际的入口点。一个这样的例子是 VxHunter[122] ，适用于许多带
有 VxWorks 操作系统和编译工具链的固件。

### 7.5 确定内存布局   

​	确定内存布局可以配置处理器布局中不同类型内存的位置。设置RAM，Flash 和 MMIO 的地址。如果系统有规格，通常可以从数据表中确定内存布局。对于ARM 系统，可能有一个 CMSIS-SVD ⽂件定义了内存布局。这些⽂件采用特定格式，可以加载到逆向工程 (RE) 工具中，例如Ghidra 或 IDA，并且可以运行⾃动分析来更新内存布局[97]。有时能指定内存布局的其他类型⽂件包括 EDC ⽂件和 hwconf ⽂件。

​	如果没有⽂档并且 RE 工具失败，则对正在模拟的设备上的组件进行物理检查可能有助于确定内存布局。通常，零件上的标记和制造印刷可用于识别零件的数据表。如果内存布局仍然未知，则模拟器可以过度配置内存(例如，提供比物理系统更多的内存)并且必须通过反复试验和错误来确定代码的位置以及外设的位置，然后这些交互通常映射到内存映射的IO。在所调研的工具中，内存布局必须由从业者指定，尽管在某些情况下工具会让这个过程更容易，例如当存在 CMSIS-SVD ⽂件时的 Ghidra 。

### 7.6 识别处理器和/或板的支持包 (BSP)

​	根据从业者目标的精度，通常不一定需要识别确切的处理器。例如与QEMU 的情况一样，模拟器仅适用于ISA级别的功能。对周期级别的仿真精度，例如gem5 [12]等工具需要知道确切的处理器，因为相同的 ISA 指令可以在不同处理器的周期级别以不同的⽅式实现。在QEMU中，如果没有使用多功能的机器，即使指定的 ISA 是正确的也可能会出现错误。

​	如果从业者需要解决识别处理器的挑战， 解决内存布局可能会有所帮助。但是，如果处理器是已知的，通常处理器的⽂档会指定内存布局。如果你可以访问硬件，处理器很可能会被标签化，从而解决挑战。

​	如果您无法访问硬件，则从业者可以进行激进的指令查找来分析指令如何与内存交互，然后可以将此分析与已知处理器上的分析进行比较，以缩小处理器候选范围。这仍然是从业者选择和测试不同处理器的手动过程。如果供应商是已知的，则可以利用来⾃其他逆向固件项目的已知信息[138]。如果之前的分析不成功，从业者可以运行暴力脚本来测试基础模拟器中的所有可用处理器。如果仍然不成功，则需要访问标签硬件。对基于QEMU的调研工作，并⾮在所有情况下都严格需要识别处理器，如果QEMU的目标不是多功能的话可能需要。

### 7.7 反汇编，初始分析和 CFG 恢复

​	正如在仿真前开始时提到的，反汇编，初始分析和恢复控制流图 (CFG) 并不是严格的仿真前挑战。但是，重要的是要验证从业者所做的工作以及这一时间点所面临的挑战是否正确。在某些流程中可能会跳过对先前工作的验证，但我们发现在继续进行下一阶段之前进行验证将节省时间并减少仿真阶段的麻烦。例如，当存在多种可能的入口点时(当除了正在分析的主固件之外还有引导加载程序)，进行验证和确认入口点是否正确值得额外的时间来节省从业者去浪费时间重新托管可能无法回答手头仿真问题的引导加载程序。

​	如果从业者知道固件在做什么，例如硬件已知的情况，分析 CFG 可以帮助确认先前的挑战，如基地址，ISA，入口点是否正确。当CFG被恢复时，还传达了反汇编器和反编译器的执行情况。这将使您了解仿真执行可能的成功程度和/或仿真继续后的精度。在所调研的工作中，核心反汇编器相对较少。angr 使用 capstone 作为其反汇编程序的一部分，而 QEMU 和 Ghidra 使用 C/C++ 来实现它们的反汇编。其他调研的工具使用的基础模拟器会重用这些反汇编实现之一。如果反汇编程序运行的好，则说明已正确反汇编指令，并且如果控制流不离开主处理器，则可以轻松恢复 CFG。


​	如果控制流离开主处理器并进入协处理器芯片(例如，GPU 或 DSP)进行初始化，则几乎不可能恢复协处理器中发生的 CFG 。此时从业者可以通过比较控制权传递给协处理器前后的内存状态来尝试确定发生了哪些状态和内存变化。如果控制流离开模拟处理器，则重新托管的那部分模拟精度会受到限制，但在大多数情况下不是模拟的限制因素。


​	如果硬件未知并且从业者不知道固件试图执行的控制逻辑，反汇编和分析仍然可以用于验证。反汇编将确保知道正确的 ISA，并可能有助于验证处理器。仍然可以通过创建和分析控制流图来确定整图中是否存在有效流，⾄少可以保证基地址和入口点是正确的。

​	反汇编和分析是迭代的，这意味着从业者将执行反汇编，然后在迭代中分析结果，在迭代之间，他们将根据分析结果稍微修改反汇编的输入和参数。这个过程将一直持续到分析结果符合从业者的预期。在此迭代过程中，将对 ISA，处理器，基地址，入口点，内存布局等进行调整。 为了巩固ISA，处理器和内存布局，从业者可以同时分析同一系统的多个固件，聚合分析结果以巩固结果。

## 8 仿真

​	在图 3 中，我们展示了从业者通常会在仿真设置和重新托管固件的执行阶段探索的流程。这些阶段通常会在仿真器开发的迭代中穿插。这些挑战通常不是线性的，而是根据重新托管的固件以不同的顺序发生。

​	在从业者确定固件的处理器，内存布局，入口点和基地地址之后，将在基本模拟器中验证支持情况。理想状态下，基础仿真器对处理器有支持，如果没有，执行者将不得不为基础仿真器创建一个新的规格实现。QEMU和 Ghidra 有关于如何添加对新处理器的支持的说明[114,171]。在基本模拟器可用之后，下一个挑战就可以解决了。

​	通过克服仿真前的挑战，可以将固件加载到仿真器中并开始执行。为了使仿真执行贴近真实的系统，还必须克服额外的挑战。我们把这些挑战分成子类别—设置和执行。设置的挑战通常在模拟器暂停或停止时静态解决，而执行挑战则在模拟器实际运行时解决，作为对从业者的参考，我们提供表二，但不提供执行的参考表，因为所有调研的工作都试图克服给定的挑战。

### 8.1 仿真设置

​	在您克服仿真前的挑战之后，现在您需要确定如何处理配置，外部交互和内存。仿真设置包含这些问题，并与实际仿真执行密切相关。设置通常在执行阶段之间进行迭代，每次迭代都会获得更多关于固件及其对仿真器和外设的依赖关系的信息。仿真器得到改进，固件也会被迭代地执行。这种情况一直持续到分析师试图解决的问题得到解答。

​	 如 4.1 节所述，仿真的范围可能从单芯片或微控制器到子系统或大型分布式系统。仿真的范围将影响仿真过程中面临的挑战。我们在此讨论的问题并⾮包罗万象，但我们相信这⾜以说明当前在仿真设置阶段面临的主要挑战。

*8.1.1 外设，外部硬件，和建模*。处理外设访问是目前大量研究的重点。由于外设和供应商的多样性，固件访问的外设很可能未在基础仿真器中实现。处理外部硬件和外设交互包括如何处理或表示仿真器和外设之间的交互。如第 7.5 节所述，静态确认内存映射将指定外设所在的位置，而不会知道外设是否使用或何时使用。动态分析和执行可以提供其中的一些信息，在分析给定外设子集的有效输入时发现使用了哪些外设。根据外设模型的精度，即外设建模的程度，将动态执行限制到特定路径。提供更逼真的外设模型将增加执行的代码量，并导致对更多不会用到的外设的有效访问。在动态执行过程中，如果模拟器由于尝试访问未映射的外设而出现异常或崩溃，这通常发生于存在别名问题时。解决别名问题是静态可行的，但也可能必须在执行期间通过跟踪各种地址并使用此信息更新仿真器的外设模型来动态解决。

​	处理外设交互可以通过模拟外部设备来解决，使用硬件加入循环 (HITL) 或给固件打补丁以绕过交互来完成。固件修改可能会忽略其中一些访问(例如设置 CPU 时钟频率)，总是给出一个递增的值，或从预期输入的⽂件中读取。重要的操作将部分取决于重新托管的固件。使用 HITL 将为执行和内存提供最高精度，但会禁止仿真的并行性，因为受限于所连接硬件的可用性。HITL 仿真在同步仿真器和硬件之间的状态⽅面面临着重大挑战。例如，计时器可能会在硬件上产生中断，导致在持续处理计时器的 ISR 时一直卡住，而仿真器没有计时器，因此不处理任何中断。另一个例子是看门狗计时器会在分析人员慢慢模拟或使用调试器(例如 GDB)时产生不断的中断。

​	提供抽象需要人工介入和对外设执行全面的了解，但也确实提供了高精度。使用这种⽅法的工具包括QEMU，HALucinator，PANDA 和 Muench2018。另一⽅面，使用模糊测试器不需要精确到设备的知识，但可能无法为您想要回答的许多问题提供⾜够的精度，并且通常仅有助于发现错误和漏洞。P^2^IM 使用模糊测试⽅法并成功地发现一些漏洞。像 Pretender 那样使用机器学习的方法很有吸引力，虽然精度可能会受到轻微影响，但除了简单的软件缺陷之外，仍然可以提供有价值的见解。目前机器学习⽅法的结果仅在简单外设(例如，串行/UART 端口是最复杂的)上得到概念的证明，并且尚不清楚该⽅法是否适用于任意硬件外设。

​	在其他调研的工具中，Firmadyne 和 CostinFA 假设外设是其核心内核的一部分，否则仿真器将崩溃。他没有提供围绕这些挑战的相应技术。SURROGATES 和 PROSPECT 将外设访问转发到实际硬件来绕过建模，但会引入仿真挑战的状态同步和延迟。

*8.1.2 内存交互和配置*。大多数仿真器允许从业者指定数据，代码和外设的位置。这样做允许仿真器设置仿真的限制，例如在尝试执行数据崩溃时通知从业者。仿真配置通常允许指定不同的内存区域和交互类型，例如 RAM，闪存和内存映射的外设所在的位置。此仿真配置也可以描述为配置仿真器，这是启动仿真器所必需的。配置将包括提供在仿真前期间发现的信息，包括指定基地址和入口点以及内存布局和可用内存量。

​	如果一个工具没有内置对内存的支持，那么重用基础模拟器的支持或通过软件提供交互对从业者来说是可行的选择。在 SURROGATES 和 PROSPECT 等特殊情况下，可以将交互转发到实际硬件。对HALucinator，Ghidra，PANDA，PROSPECT 和 Muench2018，预计从业者将使用内置的内存处理程序或提供模块以允许内存交互正确执行。对于 Firmadyne 和 CostinFA，内存和内存处理直接内置于内核中，因此如果内存或交互出现⾮固件缺陷的错误，则需要修改其内核。

*8.1.3 配置硬件*。配置硬件对于使用 HITL 的仿真是一项挑战。这需要初始化硬件状态以便可以使用，然后将其带入循环。为此从业者需要在模拟器中指定如何以及何时将交互转发到硬件。这可能还需要延迟或使用具有⾜够快的执行速度的专用硬件才行，如 SURROGATES 或 Aveksha [161] 的情况，Aveksha 是一种适用于嵌入式无线节点的空间和时间的高粒度的⾮侵入式执行跟踪系统。Avatar^2^是配置硬件时的强大工具，提供了最接近于集成在 HITL 的即插即用框架。

*8.1.4缺失代码*。在某些情况下，当您恢复 CFG 时，您可能会注意到缺少代码。缺少代码通常发生在指定错误的入口，或修补了可能编写代码的功能(例如引导加载程序) ，固件不完整(例如，部分固件更新或解包仅部分成功)时，或设备中的 ROM 芯片上有代码。如果固件是从实际的硬件设备上获取的，那么缺少代码的情况就更常见。在某些情况下，缺失的代码是可以被修补的并仍然能获得从业者想要的仿真精度水平。另一种可能，缺失的代码可能会使仿真不可行。

​	如果模拟器试图执行缺失的代码，通常系统会抛出异常或大家一起崩溃。我们知道克服这一挑战的唯一技术是用模型替换代码或完全跳过代码。HALucinator 通过使用函数拦截技术替换代码，然后允许用手动编写的模型替换此类功能。对其他调研的工具，缺少代码将需要某种手动干预来克服，例如与 HALucinator 类似的手动提供功能。

*8.1.5 功能识别和标签化*。功能识别对某些仿真技术是必要的但不是全部。如果仿真精度处于模块或功能级别，则从业者可能希望确定特定的功能，例如硬件抽象库 (HAL) 调用，并为这些功能提供抽象。功能识别不是一个简单的问题，是大量论⽂的基础，仍然是一个⾮常活跃的研究领域[4，8，21，29，78，93，100，109，110，132，133，149，180]。

​	在调研的工作中，angr，Ghidra 和 HALucinator 的框架中有内置库匹配。其他工作要么不需要克服这些问题，要么没有解决功能识别问题。angr，Ghidra 和 HALucinator 将使用现有技术来尝试识别某些功能，例如使用 IDA FLIRT 签名，加载库并尝试匹配，编译现有 HALs 并将重新托管的固件与数据库进行比较。其他技术包括从副作用识别功能，例如 Sibyl [152]。

### 8.2 仿真执行

​	执行解决如[11]中提到的经典仿真问题，并扩展到包括随着仿真使用的增多而出现的问题。不同的执行精度也将使用不同的仿真技术。周期精确的工具，例如gem5 [12]，将根据处理器对指令进行解码并在模拟指令时使用与原始处理器相同的 CPU 流水线深度和阶段。Simics [105]具有指令级精度，因此将在每条指令之后解码指令并更新状态。QEMU 使用基础块精度，并使用微型代码生成器 (TCG) 将目标架构的基础块指令转换为的对应主机机器指令。Ghidra 模拟器将指令翻译成 P 代码，每条机器指令翻译后生成多达30条 P 代码指令。其他的将使用各种其他中间表示，包括 LLVM IR [95]，VEX [115]，REIL[54]，BAP 的 BIL [18]，Binary Ninja LLIL[173]等。在调研的工具中，angr 将指令转换为 VEX。 这些技术中的每一种都有⾃⼰的挑战和权衡，这就是为什么在这一领域仍有研究的原因。
​	对每个调研的工具，都有一个基础模拟器。angr，Avatar2 ，CostinFA，Firmadyne，HALucinator，P^2^IM，PANDA，Pretender，PROSPECT，SURROGATES 和 Muench2018 都使用 QEMU 作为基础模拟器。angr 还使用 CLE 加载器来允许 Avatar2目标通过他们称为 angr_symbion 的框架在其架构中详细运行，该⽅法结合了符号执行和具体执行。交叉工具的集成本质上为任何模拟器打开与任何其他分析工具，符号引擎或模糊测试器一起工作的大门。对于传统的执行挑战(下面的不同小节)，基础模拟器通常会解决并克服挑战。因为基础模拟器解决了整个执行挑战中的挑战，我们没有具体说明每个工具为克服给定挑战所做的工作，而是更普遍地说明 QEMU，Simics 和 Ghidra 模拟器所做的工作。

### 8.2.1寄存器分配 

​	每种架构都有不同的寄存器和不同的约定。例如，不同架构上的程序计数器 (PC) 都是不同的寄存器，例如 ARM 上的R15 ，x86/x86‑64/PowerPC 上的 PC，TI‑MSP430 上的 R0 。大多数仿真器会将每个寄存器映射到主机上具体的固定内存地址或寄存器。将寄存器映射到固定的主机内存是通用的，可能是最便携的解决⽅案，适用于QEMU，Simics 和Ghidra 仿真器。

*8.2.2 直接块链接。* 块链接与 QEMU 直接相关，因为 Simics 进行子指令级别的仿真和执行，使得如果指令不修改控制流，则贯穿和链接⾃然会发生。Ghidra 将允许⾃然的贯穿，但允许在其 UI 中修改控制流。

​	如果模拟器进行翻译，例如 QEMU 和整个基础块的翻译，那么模拟器有一个模拟的程序计数器，用于查找下一个要执行的代码块。这些块通常缓存在内存中以加快执行速度，因此需要在哈希表中查找以检索正确的块。对于某些仿真器，人们会在块的末尾添加指令以直接链接到下一个要执行的指令块。

*8.2.3 ⾃行修改代码和翻译代码失效*。在某些CPU 上，⾃行修改代码不是问题，因为在修改代码时会执行特定的代码缓存失效指令。但是在其他 CPU 上可能没有无效指令，因此这变得更加困难。在[11] 中，他们通过跟踪翻译后的代码和相应的只读主机页来处理⾃我修改代码。如果对代码进行写入，则人们会使翻译后的代码无效，从而允许重写代码。他们在使用软件 MMU 时会做一些更聪明的事情，因为在仅更改数据时他们不总是使代码无效。Ghidra 仿真器具有写入权限，并且代码是只读的。因此，如果写入代码，模拟器将抛出错误并退出。目前尚不清楚 Simics 如何或是否支持⾃行修改代码。

*8.2.4 ⾮易失性存储器。*与闪存和其他硬盘相比，⾮易失性存储 (NVM) 因其延迟和功耗效率得到了改善而变得越来越流行。传统上 NVM 并不是需要面临的挑战，因为他最近才变得越来越普遍。由于 NVM 相对较新，在具有 NVM 的板上，大多数当前的系统仿真器可能都缺乏支持。一些供应商提供了有关如何启用模拟环境的说明，您可以在其中构建持久内存 (PMEM) 应用程序而无需实际硬件。为了克服系统仿真中的 NVM 或 PMEM 挑战，您需要确定内存如何交互并为其提供处理程序，就像 QEMU 对普通内存所做的那样。不同 PMEM 硬件供应商给出的关于如何模拟其硬件的说明在实现过程中⾄关重要。QEMU，Simics 和 Ghidra 仿真器通过在主机系统中分配内存并由分析师对硬件交互建模来处理 NVM [46]。对于 QEMU，一些工具提供了交互，例如NVRAM [58]。

*8.2.5 直接内存访问(DMA)。*在某些情况下，协处理器可能会使用直接内存访问(DMA) 进行初始化，或外设可能会直接写入内存。解决 DMA 挑战可以通过模拟协处理器，使用 HITL 模拟，或者如果固件使用已知的 DMA 函数调用，也可以通过拦截和替换来实现功能。Avatar2 在软件中扩展了QEMU，SURROGATES 使用了硬件，以允许将外设访问转发到实际硬件。Simics 通常使用一种更⾃定义的解决⽅案，并且还允许在给定的修改后进行 HITL。HALucinator 将通过拦截执行 DMA 的 HAL 调用来处理 DMA，并将通过手动实现功能来完成所需的内存修改。

*8.2.6 处理中断。*有多种⽅法可以处理中断。处理中断是另一个活跃的研究领域，因为有多种⽅法可以完成中断处理。仿真器可能会或可能不会检查每条指令或翻译块的中断，并权衡不同的实现。在某些情况下，模拟器可能要求用户触发中断，调用中断处理程序，或修改固件中特定部分的⾃动调用。这些技术中的每一种都有优势和劣势。对于使用 QEMU 的，他们不会在每个翻译块检查是否有硬件中断挂起，而是依赖于用户异步调用特定函数并指定挂起状态的中断处。这时函数重置当前正在执行的翻译块的链接，确保执行进入 CPU 仿真的主循环。在这个主循环中， 查看是否有硬件中断挂起。通过要求用户异步中断调用并在基础块之后检查中断，允许仿真器以更少的开销更快地运行，同时仍然支持中断。相比之下，Simics 将允许每条指令之间的中断，从而以速度为代价换取更高的执行精度。Ghidra 仿真器还可以在不同 P 代码操作之间产生子指令级别的中断。


​	在这两种情况下，如果您正在测试诸如中断⻛暴(发送具有不同顺序和频率的多个中断)之类的东西，您很可能必须异步指定基础模拟器的中断。当处理这些中断时可能会影响系统的精度。

*8.2.7 多线程。*在某些系统中，启用了多线程。使用多线程的应用程序可能会使用锁/信号量进行线程间通信，但这要求您的系统模拟器允许多个线程同时“运行”。如果系统允许运行多个应用程序，它们将使用调度程序并使用抢占式或协作式调度。协作调度中模拟器本质上只需要允许多线程执行，并且这些线程可以在交互期间管理⾃⼰。抢占式调度需要触发中断以使调度程序运行并在线程之间执行上下⽂切换。在仿真过程中，从业者通常会从单线程应用程序开始，一次运行一个，随着系统获得更多功能和精度，逐渐增加复杂性和线程数。


​	正如通篇所提到的，由于指令级精度的能力，Simics 因在调试多线程固件或系统时更有用而闻名。QEMU能够模拟这样的系统，但在某些情况下可能会产生程序运行正确的错觉，因为线程只能在 QEMU 的基础块的末尾进行交互，而 Simics 和 Ghidra 仿真器允许任何指令对之间的线程交互，甚至可以在一个基础块的中间。QEMU 可以尝试通过在每条指令处设置断点来克服这一限制，这实际上使每条指令成为一个基础块，但这会大大降低系统运行速度甚至让仿真变得没有用处。

*8.2.8 调试。*在系统仿真期间，毫无疑问您会遇到一些错误，无论是实际固件和/或系统中的错误，还是您的仿真器存在错误和缺陷。无论哪种情况，您都需要能集成一些调试。GNU GDB 是集成到工具中的普遍的选项，它为一些主要软件提供了插件，包括 Avatar2，angr，Ghidra，QEMU 等。我们发现另一个有用的选项是首先模拟串行端口或其他打印消息的形式，从而启用 “printf” ⻛格的调试。除打印之外，直接将函数调用插入固件对调试很有用。


​	QEMU 与连接到一起的调试器一样工作，记录和设置一些断点，这对于使用过 GDB 的人来说应该很熟悉。Ghidra 仿真器还允许连接 GDB 桥以进行调试。Simics 支持集成调试器，可提供正向和逆向调试。

*8.2.9 时序约束。*如果您的仿真目的是回答有关时序的问题，您可能会受到使用的仿真⽅法和工具的限制。在某些情况下，您可以使用硬件集成解决⽅案(例如 SURROGATES)，使用专门的硬件进行转发内存或外设访问到实际设备。如果您需要并行仿真，HITL可能不可行。在这种情况下，从业者可能不得不实现他们⾃⼰的时序。实现的时序可以是周期，指令，基础块或类似的东西。在许多情况下，时序与启用中断密切相关，因为某些中断是时序或看门狗中断，在这些情况下需要在仿真器中进行某种触发或时序实现。

​	如果基础仿真器是 Simics，则仿真器对仿真所需的周期数有更准确的理解，即使仿真器执行操作的速度较慢。相比之下，QEMU 会更快，但执行时间不准确，如果需要时序保证，这可能不合适。

##　9. 仿真后

仿真后挑战通常与仿真目的直接相关。一些挑战包括查明/修补易受攻击的代码，比较实际与想要实现的行为，发现漏洞，可视化仿真结果以及⾃动化前面的步骤以克服挑战。

### 9.1 发现漏洞

发现漏洞可以说是使用系统仿真最流行的原因。有许多技术可以发现漏洞，其中模糊测试是最流行的技术之一。

​	还有许多其他技术用于发现漏洞，包括数据流分析，污点分析，控制流分析，记录和回放执行分析，动态和符号执行。一些工具使用监督机器学习进行漏洞评估，例如 Costin等人[45]使用机器学习对固件进行分类，以帮助解决固件漏洞发掘和易受攻击的设备发掘的挑战。由于在漏洞发掘⽅面大量的工作，在本⽂中进行深入的讨论和彻底查清是不可行的，因此我们可以说这些方法存在，并请读者参考现有的评估⽂章，包括 [7，89，143]。

### 9.2 验证

一旦你有一个系统模拟器，无论是你的第一个版本的模拟器还是一个完善的版本，验证模拟器是否做到应该做的问题就会出现。虽然在整个电路系统的流片和生产之前对 SoC 芯片功能的验证进行了很好的研究[102]，但似乎缺乏对仿真系统和重新托管固件的验证。除了黑盒行为比较之外，当前的工具都没有验证它们是否正确地模拟重新托管的固件。此外，没有真正的基准测试或验证技术可以作为测试模拟器的标准。随着固件仿真作为研究领域的扩展，仿真器和重新托管的固件执行的验证是一项关键需求。

### 9.3 分析

与普通硬件/软件系统一样，仿真系统在“完成”后也进行了测试和分析。仿真期间使用的固件也被分析和测试是否存在错误。仿真器本⾝也被分析，以帮助强化在实际硬件上运行的固件。仿真后和分析包括了在⼆进制强化，漏洞检测和其他网络安全工作⽅面的大量工作。所有调研的工具都进行了某种后期仿真分析。

## 10. 考虑点

在确定要使用的基础模拟器或不同的技术/工具时，每种解决⽅案都有利有弊。在选择特定工具之前，我们主要的考虑因素包括硬件支持，精度，性能，调试支持/有效性和易用性/控制。这些考虑因素取决于从业者的意见，尽管我们试图客观地在图一中对工具的精度和⾃动化进行分类。

​	在图 4 中，我们提供了一些选择工具的决策流程图的概述，尽管我们在尝试基于调试支持，可用性和控制来缩小工具使用范围时留下了明确的路径，因为我们发现各种工具的意见差异很大。在图左边的一个大盒子里是所有被调研的工具。从那里引出的箭头给出了模拟器之间的关键区别。如果分析师对机器学习感兴趣，Pretender 将是一个很好的起点。同样，如果有兴趣使用符号执行和模糊测试来查找错误/漏洞，那么 angr，Ghidra，HALucinator 和P^2^IM 是一个很有前途的起点。如果您对使用实际硬件和仿真感兴趣，Avatar2，SURROGATES 和 PROSPECT 是合适的。除此之外，具有不同优势和用途的通用仿真器包括Panda，Muench2018，Avatar2，HALucinator，QEMU，Firmadyne，CostinFA，Ghidra，和 Simics。一旦将潜在工具缩小到一组工具后，应评估以下考虑。


​	*硬件支持*可能是过去没有使用仿真的最重要原因。模拟系统或重新托管固件的工作传统上很难与系统一起运作(有些人认为仍然如此)。这些问题正在慢慢得到改正，随着核心仿真器支持各种更多的硬件。此外，增加新硬件和处理器的工作更加简单和容易。首要考虑因素之一仍然是您要使用的工具是否支持您要模拟的硬件


​	*仿真器精度*，如第 4.3 节所述，将缩小可用于回答从业者使用仿真的问题的工具。在某些情况下，符号执行是回答手头仿真问题的可行选择。在这种情况下，angr 是受到调研的最佳选择，因为有一个专门的Slack[154]小组，提供帮助和开发渠道，对用户很友好。如果符号执行不起作用，从业者可以参考图 1 以帮助缩小可用在所需精度的调研工具范围。

​	在考虑精度之后，还需要考虑*性能和调试支持*。根据仿真的目标问题，这些属性之一将比其余的更重要。过去没有广泛使用仿真的主要原因之一是仿真器的开销和性能缓慢，但随着软件和硬件变得更加强大，性能问题正在被克服。

​	当仿真器的性能成为障碍时，部分仿真或专用硬件是可能的解决⽅案。Ghidra Emulator 允许执行部分仿真，让从业者在指定必要输入的情况下从特定功能开始仿真。SURROGATES 提供专用硬件以更快地与内存交互。HALucinator 在用户指定的在 YAML ⽂件中提供的地址上设置断点。通过只分析回答特定仿真问题所需的内存，可以提高仿真性能。


​	*调试支持*是一个需要考虑的关键属性。如果从业者在重新托管固件时对固件的功能了解有限，那么更多的调试支持将是必不可少的。如前文所述，仿真的好处之一是能够在固件重新托管期间检查每个点的内存，检查内存需要使用调试工具或日志记录。对于基础模拟器，QEMU 和 Ghidra(通过[37])具有 GDB 集成。使用 GDB，断点可被用于单步执行仿真。Simics 还具有内置调试功能，并允许断点，以及通过仿真前进和后退。需要注意的是，调试支持会影响性能，因为更多的断点和跟踪需求会减慢仿真器的速度。性能和调试支持都是研究的活跃领域，基础仿真器试图做出在监控和调试的同时更快的工具。

​	*可用性*是第 4.5 节中提到的探索和控制以及工具⾃动化的组合。如果一个工具是⾃动的，执行速度⾜够快，并且可以回答手头的仿真问题，那么该工具显然是最好的选择。然而，目前这并不是一个常见的场景，因此从业者对探索和执行重新托管的固件有多少控制权是一个必须考虑的属性。与 GDB 集成的工具允许扩展的控制。使用模糊或随机化的工具控制权较低，在某些情况下可能对从业者有用，也可能没用。本质上所有调研的工具中，调试都内置在基础模拟器中和/或通过插件或默认内置的工具提供。

##　11. 总结与结论

由于从业者正在考虑进行固件重新托管和系统仿真的合适的工具，我们建议使用我们在此处介绍的仿真比较技术和考虑点。我们已经讨论了通过仿真工具采用的技术，工作的系统类型，工具的目的，工具可以进行多少的探索，以及最有用的分类技术-精度来比较仿真工具的方法。

​	除了比较技术，我们还展示了所调研的工作的分类，包括angr，Avatar2 ，CostinFA，Firmadyne，Ghidra，HALucinator，P^2^IM，PANDA，Pretender，PROSPECT，QEMU，Simics，SURROGATES 和 Meunch2018。这些技术及其差异已经被讨论过了，通过介绍在仿真和固件重新托管和期间面临的核心挑战，为新的从业者或研究人员提供了一个关于在固件重新托管和仿真领域该从哪里开始和进行进一步的研究的概述。当前工具中的⾃动化大大降低了系统的精度，但在许多情况下仍然⾜以回答一些仿真问题。⾃动化当前工具的工作是困难的，因为嵌入式系统的广阔的覆盖面，但我们注意到一些调研的工具所做的努力，以及认识到了使工具更容易和⾃动化以供从业者使用的重要性。
​	从最初开始，系统仿真和固件重新托管已经发生了巨大的变化和发展。尝试模拟系统/重新托管固件时面临的挑战随着模拟的改进而改善。本⽂强调需要进一步的研究和工具来解决仿真前 仿真和仿真后的挑战。由于工具和解决⽅案的发明和发布，我们提供了如何评估这些工具的分类和标准。
