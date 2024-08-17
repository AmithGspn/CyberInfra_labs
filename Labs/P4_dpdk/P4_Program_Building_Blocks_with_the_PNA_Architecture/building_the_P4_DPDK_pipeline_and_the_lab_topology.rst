Building the P4-DPDK pipeline and the lab topology
==================================================

This section shows the steps required to run the P4-DPDK along with building 
the lab topology. The step-by-step procedure is explained in detail in the previous
lab. In this lab, the procedure is automated.

Running the P4-DPDK pipeline
++++++++++++++++++++++++++++

Now that all the required scripts are prepared, we can run the pipeline.

**Step 1.** Click on the terminal tab in the start bar to maximize the window.

.. image:: images/16.png

**Figure 16:** Maximizing Linux terminal window.

**Step 2.** Issue the command ``sudo su`` on the terminal to enter root mode. When prompted for a password, type ``password`` and hit enter. Note that the password will not be visible as you type it::

    sudo su

.. image:: images/17.png

**Figure 17:** Entering root mode.

**Step 3.** Navigate to the lab2 directory using the cd command::

    cd P4DPDK_labs/lab2  

.. image:: images/18.png

**Figure 18:** Navigating to the lab2 directory.

**Step 4.** Build and run the pipeline by typing the following command::

    ./run_pipeline.sh

.. image:: images/19.png

**Figure 19:** Running P4-DPDK pipeline.

The run_pipeline.sh script is a shell script that automates the process of running the P4-DPDK pipeline. 

Building the lab topology
+++++++++++++++++++++++++

**Step 1.** Click on the new tab button at the top left of the terminal while running the pipeline.

.. image:: images/20.png

**Figure 20:** Opening a new terminal in a new tab.

**Step 2.** Issue the command ``sudo su`` on the terminal to enter root mode. When prompted for a password, type ``password`` and hit enter. Note that the password will not be visible as you type it::

    sudo su

.. image:: images/21.png

**Figure 21:** Entering root mode.

**Step 3.** Navigate to the lab2 directory using the cd command::

    cd P4DPDK_labs/lab2  

.. image:: images/22.png

**Figure 22:** Navigating to the lab2 directory.

**Step 4.** Build the lab topology by typing the following command::

    ./set_topology.sh

.. image:: images/23.png

**Figure 23:** Setting the lab topology.

The *set_topology.sh* script is a shell script that automates the process of building the lab topology. Two namespaces are built and configured in this step with a virtual device linked to each.