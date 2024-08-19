CyberInfra Labs
===============

.. CyberInfra labs documentation master file, created by
   sphinx-quickstart on Sun Jul 28 16:19:51 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. image:: NSF.png
   :align: center

.. raw:: html

   <p style="text-align: center;">
      <em>Award 2417823</em> 
      <br></br>
      <em>“Collaborative Research: CyberTraining: Implementation: Medium: CyberTraining on Accelerating Infrastructure Workloads using Next-Generation SmartNICs/DPUs”</em>
   </p>

Network packet processing faces significant performance challenges due to kernel overheads. These issues have become more pronounced with the rapid growth of network traffic. 
To address these challenges, the Data Plane Development Kit (DPDK) was developed. DPDK bypasses the kernel and operates directly in user space, offering significant improvements 
in performance and latency for packet processing tasks. However, DPDK's steep learning curve presents a barrier to entry for developers and network administrators. In recent 
years, P4 has emerged as a language specifically designed for expressing packet processing data paths. Building on this development, P4-DPDK has been introduced as a new technology 
that bridges P4 and DPDK. It allows developers to create P4 code which is then translated into a DPDK pipeline, combining the expressiveness of P4 with the performance benefits 
of DPDK. This lab series offers a hands-on experience on the basics of P4-DPDK.

.. toctree::
   :maxdepth: 2
   :caption: VM Installation:

   Labs/VM_Installation/vm_installation

.. toctree::
   :maxdepth: 2
   :caption: Labs:

   Labs/P4_dpdk/Introduction_to_P4_DPDK/Introduction_to_P4_DPDK
   Labs/P4_dpdk/P4_Program_Building_Blocks_with_the_PNA_Architecture/P4_Program_Building_Blocks_with_the_PNA_Architecture
   Labs/P4_dpdk/PNA_Parser_Implementation/PNA_Parser_Implementation
   Labs/P4_dpdk/Introduction_to_Match_action_Tables_(Part_1)/Introduction_to_Match_action_Tables_(Part_1)
   Labs/P4_dpdk/Introduction_to_Match_action_Tables_(Part_2)/Introduction_to_Match_action_Tables_(Part_2)
   Labs/P4_dpdk/Populating_and_Managing_Match_action_Tables_at_Runtime/Populating_and_Managing_Match_action_Tables_at_Runtime
   Labs/P4_dpdk/Checksum_Recalculation_and_Packet_Deparsing/Checksum_Recalculation_and_Packet_Deparsing
