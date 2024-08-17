Introduction
============

Longest prefix match (LPM)
~~~~~~~~~~~~~~~~~~~~~~~~~~

Table 2 is an example of a match-action table that uses LPM [1]_ [2]_. 
Assume that the key is formed with the destination IP address. 
If an incoming packet has the destination IP address 172.168.3.5, 
two entries match. The first entry matches because the first 29 
bits in the entry are the same as the first 29 bits of the destination 
IP. The second entry also matches because the first 16 bits in the 
entry are the same as the first 16 bits of the destination IP. The 
LPM algorithm will select 172.168.3.0/29 because of the longest 
prefix preference.

.. [1] The P4 Language Consortium, “P4 Portable NIC Architecture (PNA)”, Version 0.5, 2021. 
    [Online]. Available: https://p4.org/p4-spec/docs/PNA.html

.. [2] The P4 Language Consortium, “P4 Portable Switch Architecture (PSA)”, 2021. 
    [Online]. Available: https://p4.org/p4-spec/docs/PSA.html#sec-match-kinds

.. table:: Table 2: Match-action table using LPM as the lookup algorithm.
   :align: center
   
   ==============  ===========  =========================
   Key             Action       Action data
   ==============  ===========  =========================
   172.168.3.0/29  forward      port 1,
                                macAddr=00:00:00:00:00:01
   ==============  ===========  =========================
   172.168.3.0/29  forward      port 2,
                                macAddr=00:00:00:00:00:02
   ==============  ===========  =========================
   default         drop
   ==============  ===========  =========================

Figure 1 shows the main control block portion of a P4 program [3]_ [4]_. Two actions 
are defined, ``drop`` and ``forward``. The ``drop`` action (lines 6 - 8) invokes the 
``drop_packet`` function, causing the packet to be dropped. The ``forward`` action 
(lines 9 - 12) accepts as input (action data) the destination MAC address and 
port. These parameters are inserted by the control plane and updated in the 
packet during the ingress processing. In line 10, the P4 program assigns the 
egress port defined by the control plane as an input to the ``send_to_port`` extern 
function. It is used to direct a packet to a specified network port. Line 11 
assigns the destination MAC address passed as a parameter to the packet's new 
destination address. Lines 13-23 implement a table named ``forwarding_lpm``. The 
table matches the destination IP address using the LPM type. The actions associated 
with the table are ``forward`` and ``drop``. The default action invoked when there is a 
miss is the drop action. The maximum number of entries is defined by the programmer 
(i.e., 1024 entries, see line 21). The control block starts executing from the apply 
statement (see lines 24-28) which contains the control logic. In this program, the 
``forwarding_lpm`` table is activated in case the incoming packet has a valid IPv4 header.

.. [3] P4lang, “pna”, 
    [Online]. Available: https://github.com/p4lang/pna/tree/main?tab=readme-ov-file

.. [4] “p4c core.p4”. 
    [Online]. Available: https://github.com/p4lang/p4c/blob/main/p4include/core.p4.

.. image:: images/Generic_workflow_design.png

**Figure 1:** Main control block portion of a P4 program. The code implements a match-action 
              table with LPM lookup.