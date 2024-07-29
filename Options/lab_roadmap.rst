
Lab roadmap
===========

This lab is organized as follows: 

#. Section 1: Introduction.
#. Section 2: Lab topology.
#. Section 3: Loading the P4 program.
#. Section 4: Configuring switch s1.

Introduction
------------

Since the emergence of the world wide web and the explosive growth of the Internet in the 1990s, 
the networking industry has been dominated by closed and proprietary hardware and software. The 
progressive reduction in the flexibility of protocol design caused by standardized requirements, 
which cannot be easily removed to enable protocol changes, has perpetuated the status quo. This 
protocol ossification1, 2 has been characterized by a slow innovation pace at the hand of few 
network vendors. As an example, after being initially conceived by Cisco and VMware3, the 
Application Specific Integrated Circuit (ASIC) implementation of the Virtual Extensible LAN (VXLAN)4, 
a simple frame encapsulation protocol, took several years, a process that could have been reduced to 
weeks by software implementations. The design cycle of switch ASICs has been characterized by a 
lengthy, closed, and proprietary process that usually takes years. Such process contrasts with the 
agility of the software industry. 

The programmable forwarding can be viewed as a natural evolution of Software-Defined Networking (SDN), 
where the software that describes the behavior of how packets are processed, can be conceived, tested, 
and deployed in a much shorter time span by operators, engineers, researchers, and practitioners in 
general. The de-facto standard for defining the forwarding behavior is the P4 language5, which stands 
for Programming Protocol-independent Packet Processors. Essentially, P4 programmable switches have 
removed the entry barrier to network design, previously reserved to network vendors.

Workflow of a P4 program
~~~~~~~~~~~~~~~~~~~~~~~~

Programming a P4 switch, whether a hardware or a software target, requires a software development 
environment that includes a compiler. Consider Figure 1. The compiler maps the target-independent P4 
source code (P4 program) to the specific platform. The compiler, the architecture model, and the 
target device are vendor specific and are provided by the vendor. The P4 source code on the other 
hand is supplied by the user.

The compiler generates two artifacts after compiling the P4 program. First, it generates a data plane 
configuration (Data plane runtime) that implements the forwarding logic specified in the P4 input 
program. This configuration includes the instructions and resource mappings for the target. Second, 
it generates runtime APIs that are used by the control plane / user to interact with the data plane. 
Examples include adding/removing entries from match-action tables and reading/writing the state of 
extern objects (e.g., counters, meters, registers). The APIs contain the information needed by the 
control plane to manipulate tables and objects in the data plane, such as the identifiers of the 
tables, fields used for matches, keys, action parameters, and others. 

.. image:: ../images/Generic_workflow_design.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 1:</strong> Generic workflow design. The compiler, the architecture model, and the target switch 
   are provided by the vendor of the device. The P4 source code is customized by the user. The compiler 
   generates a data plane runtime to be loaded into the target, and the APIs used by the control plane 
   to communicate with the data plane at runtime.</p>

Workflow used in this lab series
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This section demonstrates the P4 workflow that will be used in this lab series. Consider Figure 2. 
We will use the Visual Studio Code (VS Code) as the editor to modify the *basic.p4* program. Then, we 
will use the p4c compiler with the V1Model architecture to compile the user supplied P4 program 
(*basic.p4*). The compiler will generate a JSON output (i.e., basic.json) which will be used as the 
data plane program by the switch daemon (i.e., simple_switch). Finally, we will use the 
``simple_switch_CLI`` at runtime to populate and manipulate table entries in our P4 program. The target 
switch (vendor supplied) used in this lab series for testing and debugging P4 programs is the 
behavioral model version 2 (BMv2)6. 

.. image:: ../images/Workflow_used_in_this_lab.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 2:</strong> Workflow used in this lab series</p>


Lab topology
------------

Let’s get started with creating a simple Mininet topology using MiniEdit. The topology uses 10.0.0.0/8 
which is the default network assigned by Mininet. 

.. image:: ../images/MiniEdit_shortcut.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 3:</strong> MiniEdit shortcut</p>

**Step 1.** A shortcut to MiniEdit is located on the machine’s desktop. Start MiniEdit by double-clicking 
on MiniEdit’s shortcut. When prompted for a password, type password.

.. image:: ../images/Lab_topology.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 4:</strong> Lab topology</p>

**Step 2.** On MiniEdit’s menu bar, click on File then Open to load the lab’s topology. A window will emerge. 
Open the folder called lab2, select the file lab2.mn, and click on Open.

.. image:: ../images/Opening_a_topology.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 5:</strong> Opening a topology in MiniEdit</p>

**Step 3.** The network must be started. Click on the Run button located at the bottom left of MiniEdit’s 
window to start the emulation. 

.. image:: ../images/running_the_emulation.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 6:</strong>  Running the emulation.</p>

Verifying connectivity between host h1 and host h2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Step 1.** Hold the right-click on host h1 and select Terminal. This opens the terminal of host h1 and allows 
the execution of commands on that host. 

.. image:: ../images/Opening_a_terminal_on_host.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 7:</strong> Opening a terminal on host h1.</p>

**Step 2.** Test the connectivity between host h1 and host h2 by issuing the command below.

``ping 10.0.0.2 -c 4``

.. image:: ../images/Connectivity_test_between_h1_and_h2.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 8:</strong> Performing a connectivity test between host h1 and host h2.</p>

The figure above indicates no connectivity between host h1 and host h2 because there is no program loaded into the switch.


Loading the P4 program
----------------------

This section shows the steps required to implement a P4 program. It describes the editor that will be used to modify the P4 
program and the P4 compiler that will produce a data plane program for the software switch. 

VS Code will be used as the editor to modify P4 programs. It highlights the syntax of P4 and provides an integrated terminal 
where the P4 compiler will be invoked. The P4 compiler that will be used is p4c, the reference compiler for the P4 programming 
language. p4c supports both P414 and P416, but in this lab series we will only focus on P416 since it is the newer version and 
is currently being supported by major programming ASIC manufacturers7. 

Loading the programming environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Step 1.** Launch a Linux terminal by double-clicking on the Linux terminal icon located on the desktop. 

.. image:: ../images/shortcut_to_linux_terminal.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 9:</strong>  Shortcut to open a Linux terminal.</p>

**Step 2.** In the terminal, type the command below. This command launches the VS Code and opens the directory where the P4 
program for this lab is located.

``code P4_Labs/lab2``

.. image:: ../images/Launching_the_editor_and_opening_lab2.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 10:</strong>  Launching the editor and opening the lab2 directory.</p>

**Step 3.** Once the previous command is executed, VS Code will start. Click on *basic.p4* in the file explorer panel on 
the left hand side to open the P4 program in the editor.

.. image:: ../images/Opening_the_progtamming_environment.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 11:</strong>  Opening the programming environment in VS Code.</p>

**Step 4.** Identify the components of VS Code highlighted in the grey boxes.

.. image:: ../images/Vsc_graphical_interface.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 12:</strong>  VS Code graphical interface components.</p>

The VS Code interface consists of three main panels:

   #. Editor: the editor panel will display the content of the file selected in the file explorer. In the figure above, 
      the *basic.p4* program is shown in the Editor.
   #. File explorer: this panel contains all the files in the current directory. You will see the *basic.p4* file which contains 
      the P4 program that will be used in this lab, and the topology file for the current lab (i.e., lab2.mn).
   #. Terminal: this is a regular Linux terminal integrated in the VS Code. This is where the compiler (p4c) is invoked to 
      compile the P4 program and generate the output for the switch. 


Compiling and loading the P4 program to switch s1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Step 1.** In this lab, we will not modify the P4 code. Instead, we will just compile it and download it to the switch s1. 
To compile the P4 program, issue the following command in the terminal panel inside the VS Code.

.. image:: ../images/Compiling_p4.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 13:</strong>  Compiling the P4 program using the VS Code terminal.</p>

The command above invokes the *p4c* compiler to compile the *basic.p4* program. After executing the command, if there are no 
messages displayed in the terminal, then the P4 program was compiled successfully. You will see in the file explorer that 
two files were generated in the current directory: 

   * *basic.json*: this file is generated by the p4c compiler if the compilation is successful. This file will be used by the 
      software switch to describe the behavior of the data plane. You can think of this file as the binary or the executable 
      to run on the switch data plane. The file type here is JSON because we are using the software switch. However, in hardware 
      targets, most probably this file will be a binary file.
   * *basic.p4i*: the output from running the preprocessor of the compiler on your P4 program.

At this point, we will only be focusing on the *basic.json* file.

Now that we have compiled our P4 program and generated the JSON file, we can download the program to the switch and start the 
switch daemon.

**Step 2.** Type the command below in the terminal panel to download the basic.json file to the switch s1. The script accepts as 
input the JSON output of the p4c compiler, and the target switch name (e.g., s1). If asked for a password, type the password 
``password``.

``push_to_switch basic.json s1``

.. image:: ../images/Downloading_compiled_p4_code.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 14:</strong> Downloading the compiled program to switch s1.</p>

Verifying the configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Step 1.** Click on the MinEdit tab in the start bar to maximize the window.

.. image:: ../images/Maximizing_the_MiniEdit_window.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 15:</strong> Maximizing the MiniEdit window.</p>

**Step 2.** Right-click on the P4 switch icon in MiniEdit and select Terminal.

.. image:: ../images/Starting_the_terminal_on_s1.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 16:</strong> Starting the terminal on switch s1.</p>

.. note::
   The switch is running on an Ubuntu image started on a Docker container. Thus, you will be able to execute any 
   Linux command on the switch’s terminal. 

**Step 3.** Issue the following command to list the files in the current directory.

``ls``

.. image:: ../images/Display_contents_in_s1_directory.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 17:</strong>  Displaying the contents of the current directory in the switch s1.</p>

We can see that the switch contains the basic.json file that was downloaded after compiling the P4 program.

Configuring switch s1
---------------------

Mapping P4 program’s ports
~~~~~~~~~~~~~~~~~~~~~~~~~~

**Step 1.** Issue the following command to display the interfaces in switch s1. 

``ifconfig``

.. image:: ../images/S1_interfaces.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 18:</strong>  Displaying switch s1 interfaces.</p>

We can see that the switch has the interfaces s1-eth0 and s1-eth1. The interface s1-eth0 on the switch s1 connects to the host h1. 
The interface s1-eth1 on the switch s1 connects to the host h2. 

**Step 2.** Start the switch daemon and map the ports to the switch interfaces by typing the following command. 

``simple_switch -i 0@s1-eth0 -i 1@s1-eth1 basic.json &``

.. image:: ../images/Starting_s1_and_mapping_logical_interfaces.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 19:</strong> Starting the switch daemon and mapping the logical interfaces to Linux interfaces.</p>

.. image:: ../images/Port_mappings.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 20:</strong> Ports 0 and 1 are mapped to the interfaces s1-eth0 and s1-eth1 of switch s1.</p>

Loading the rules to the switch
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Step 1.** In switch s1 terminal, press Enter to return the CLI.

.. image:: ../images/Returning_to_s1_cli.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 21:</strong> Returning to switch s1 CLI.</p>

**Step 2.** Populate the table with forwarding rules by typing the following command. 

``simple_switch_CLI < ~/lab2/rules.cmd``

.. image:: ../images/Loading_table_entries_to_s1.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 22:</strong> Loading table entries to switch s1.</p>

The figure above shows the table entries described in the file rules.cmd.

**Step 3.** Go back to host h1 terminal to test the connectivity between host h1 and host h2 by issuing the following command.

``ping 10.0.0.2 -c 4``

.. image:: ../images/performing_a_connectivity_test_between_h1_and_h2.png
   :alt: Alternative text for the image
   :align: center

.. raw:: html

   <br><br>
   <p style="text-align: center;"><strong>Figure 23:</strong> Performing a connectivity test between host h1 and host h2.</p>

Now that the switch has a program with tables properly populated, the hosts can ping each other.

This concludes lab 2. Stop the emulation and then exit out of MiniEdit.
