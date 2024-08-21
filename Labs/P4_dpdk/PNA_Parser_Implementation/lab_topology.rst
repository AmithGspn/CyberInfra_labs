Lab Topology
============

Consider Figure 6. Two network namespaces, h1 and h2, are linked to the running P4-DPDK pipeline, 
enablingconnectivity between two namespaces. The network environments of hosts h1 and h2 are isolated 
from each other and the root namespace. 

.. image:: images/6.png

**Figure 6:** Lab topology.

Lab Settings
++++++++++++

Table 1 contains the credentials of the virtual machine used for this lab. 

.. table:: Table 2: Topology information.
   :align: center
   
   ========  =============  ==============  ==========
   **Host**  **Interface**  **IP Address**  **Subnet**
   ========  =============  ==============  ==========
   h1        dtap0          192.168.10.1    /24        
   h2        dtap1          192.168.10.2    /24
   ========  =============  ==============  ==========