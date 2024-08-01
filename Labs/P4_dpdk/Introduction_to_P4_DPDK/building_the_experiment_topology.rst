Building the experiment topology
================================

This section shows the steps required to build the experiment topology shown in the lab topology section. 
This will be done by creating two Linux namespaces and configuring the interfaces so that a connection between 
them is established.

Namespaces are a feature that partitions Linux resources. Linux namespaces provide independent instances of 
networks that enable network isolation and independent operations. Each network namespace has its own networking 
devices, IP addresses, routing tables, and firewall rules8.

Creating namespaces
+++++++++++++++++++

**Step 1.** Issue the command ``sudo su`` on the terminal to enter root mode. When prompted for a password, type 
``password`` and hit enter. Note that the password will not be visible as you type it::

    sudo su

.. image:: images/Generic_workflow_design.png

**Figure 29:** Entering root mode.

**Step 2.** Type the following command to create a namespace, h1::

    ip netns add h1

.. image:: images/Generic_workflow_design.png

**Figure 30:** Creating a namespace h1.

**Step 3.** Type the following command to create another namespace, h2::

    ip netns add h2

.. image:: images/Generic_workflow_design.png

**Figure 31:** Creating a namespace h2.

Attaching virtual device interfaces to namespace
++++++++++++++++++++++++++++++++++++++++++++++++

In this section, you will attach dtap0 to namespace h1 and dtap1 to namespace h2.

**Step 1.** Type the following command to link the interface dtap0 to the namespace h1::

    ip link set dtap0 netns h1

.. image:: images/Generic_workflow_design.png

**Figure 32:** Linking namespace h1 to *dtap0*.

**Step 2.** Type the following command to link the interface dtap1 to the namespace h2::

    ip link set dtap1 netns h2

.. image:: images/Generic_workflow_design.png

**Figure 33:** Linking namespace h1 to *dtap1*.

**Step 3.** Type the following command to activate the interface dtap0::

    ip netns exec h1 ip link set dev dtap0 up

.. image:: images/Generic_workflow_design.png

**Figure 34:** Turning up the interface, *dtap0*.

The command ``ip netns exec`` takes two arguments; the namespace and the command to run in 
the specified namespace. In the figure above, the command ``ip link set dtap0`` ``up`` will 
be executed in namespace ``h1``.

**Step 4.** Type the following command to activate the interface ``dtap1``::

    ip netns exec h2 ip link set dev dtap1 up

.. image:: images/Generic_workflow_design.png

**Figure 34:** Turning up the interface, *dtap1*.

In the figure above, the command ``ip link set dtap1 up`` will be executed in namespace h2.

