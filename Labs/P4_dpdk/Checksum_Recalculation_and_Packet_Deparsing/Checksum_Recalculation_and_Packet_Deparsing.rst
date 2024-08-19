Lab 7: Checksum Recalculation and Packet Deparsing
==================================================

This lab describes how to recompute the checksum of a header. Recomputing the checksum is 
necessary if the packet header was modified by the P4 program. The lab also describes how a 
P4 program performs deparsing to emit headers.

.. toctree::
   :maxdepth: 2
   :caption: Contents:
   :hidden:

   objectives
   lab_settings
   lab_roadmap
   introduction
   lab_topology
   implementing_the_packet_deparser_in_P4
   implementing_checksum_update_in_P4
   references