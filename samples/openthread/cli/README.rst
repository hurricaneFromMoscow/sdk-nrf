.. _ot_cli_sample:

Thread: CLI
###########

.. contents::
   :local:
   :depth: 2

The :ref:`Thread <ug_thread>` CLI sample demonstrates the usage of OpenThread Command Line Interface inside the Zephyr shell.

This sample supports optional :ref:`ot_cli_sample_thread_v12`, which can be turned on or off.
See :ref:`coap_client_sample_activating_variants` for details.

Overview
********

The sample demonstrates the usage of commands listed in `OpenThread CLI Reference`_.
OpenThread CLI is integrated into the system shell accessible over serial connection.
To indicate a Thread command, the ``ot`` keyword needs to precede the command.

The amount of commands you can test depends on the application configuration.
The CLI sample comes with the :ref:`full set of OpenThread functionalities <thread_ug_feature_sets>` enabled (:option:`CONFIG_OPENTHREAD_NORDIC_LIBRARY_MASTER`).

If used alone, the sample allows you to test the network status.
It is recommended to use at least two development kits running the same sample to be able to test communication.

.. _ot_cli_sample_diag_module:

Diagnostic module
=================

By default, the CLI sample comes with the :option:`CONFIG_OPENTHREAD_NORDIC_LIBRARY_MASTER` :ref:`feature set <thread_ug_feature_sets>` enabled, which allows you to use Zephyr's diagnostic module with its ``diag`` commands.
Use these commands for manually checking hardware-related functionalities without running a Thread network.
For example, when adding a new functionality or during the manufacturing process to ensure radio communication is working.
See `Testing diagnostic module`_ section for an example.

.. note::
    If you disable the :option:`CONFIG_OPENTHREAD_NORDIC_LIBRARY_MASTER` feature set, you can enable the diagnostic module with the :option:`CONFIG_OPENTHREAD_DIAG` Kconfig option.

.. _ot_cli_sample_thread_v12:

Experimental Thread v1.2 extension
==================================

This optional extension allows you to test :ref:`available features from Thread Specification v1.2 <thread_ug_thread_1_2>`.
You can enable these features either by :ref:`activating the overlay extension <ot_cli_sample_activating_variants>` as described below or by setting :ref:`thread_ug_thread_1_2`.

.. _ot_cli_sample_thread_certification:

Certification tests with CLI sample
===================================

The Thread CLI sample can be used for running certification tests.
See :ref:`ug_thread_cert` for information on how to use this sample on Thread Certification Test Harness.

.. _ot_cli_sample_minimal:

Minimal configuration
=====================

This optional extension demonstrates an optimized configuration for the Thread CLI sample.
The provided configurations optimize the memory footprint of the sample for single protocol and multiprotocol use.

For more information, see :ref:`app_memory`.

.. ot_cli_sample_fem_support_start

FEM support
===========

You can add support for the nRF21540 front-end module to the sample.
See :ref:`ug_radio_fem` for more information.

To add support for the nRF21540 FEM, add the provided :file:`dts-nrf21540-fem.overlay` devicetree overlay file when building.
The file is located in the :file:`samples/openthread/common` folder.
Make sure that the GPIOs in the file correspond to those in which your front-end module is connected.

.. note::
   You must add the provided overlay file if you use the nRF21540 EK.
   If you use the nRF21540 DK, build your application for the :ref:`nrf21540dk_nrf52840 <zephyr:nrf21540dk_nrf52840>` board.
   The devicetree for the nRF21540 DK already contains the required FEM configuration, so you do not need to add the overlay file.

See :ref:`zephyr:set-devicetree-overlays` for different ways of adding the overlay file.

The easiest way to add the file when building is to set it in the ``DTC_OVERLAY_FILE`` variable.
However, doing so will override the default settings.
For some boards, this sample requires additional overlay files, which are automatically included when building with the default settings.
When you set the ``DTC_OVERLAY_FILE`` variable, you must specify all overlay files that are needed for building.
Check the :file:`boards` folder to see the additional overlay files.

Follow the instructions in :ref:`cmake_options` to specify the ``DTC_OVERLAY_FILE`` variable.
For example, to build the sample from the command line for an nRF52833 DK with an attached nRF21540 EK, invoke the following command within the sample directory:
``west build -b nrf52833dk_nrf52833 -- -DDTC_OVERLAY_FILE="boards/nrf52833dk_nrf52833.overlay;../common/dts-nrf21540-fem.overlay"``

Alternatively, you can copy the contents of :file:`dts-nrf21540-fem.overlay` to the board's overlay file.

To add support for other front-end modules, add the respective overlay files in the same way.

.. note::
   Software FEM support is not present on nRF53 Series devices yet.

.. ot_cli_sample_fem_support_end

Requirements
************

The sample supports the following development kits for testing the network status:

.. table-from-rows:: /includes/sample_board_rows.txt
   :header: heading
   :rows: nrf5340dk_nrf5340_cpuapp, nrf52840dk_nrf52840, nrf52833dk_nrf52833, nrf21540dk_nrf52840

.. note::
   The multiprotocol variant is not supported on nRF53 Series devices yet.

Optionally, you can use one or more compatible development kits programmed with this sample or another :ref:`Thread sample <openthread_samples>` for :ref:`testing communication or diagnostics <ot_cli_sample_testing_multiple>` and :ref:`thread_ot_commissioning_configuring_on-mesh`.

Thread v1.2 extension requirements
==================================

If you enable the :ref:`experimental Thread v1.2 extension <ot_cli_sample_thread_v12>`, you will need `nRF Sniffer for 802.15.4 based on nRF52840 with Wireshark`_ to observe messages sent from the router to the leader board when :ref:`testing v1.2 features <ot_cli_sample_testing_multiple_v12>`.

User interface
**************

All the interactions with the application are handled using serial communication.
See `OpenThread CLI Reference`_ for the list of available serial commands.

Building and running
********************

.. |sample path| replace:: :file:`samples/openthread/cli`

|enable_thread_before_testing|

.. include:: /includes/build_and_run.txt

To update OpenThread libraries provided by the ``nrfxlib``, please invoke ``west build -b nrf52840dk_nrf52840 -t install_openthread_libraries``.

.. _ot_cli_sample_activating_variants:

Activating sample extensions
============================

To activate the optional extensions supported by this sample, modify :makevar:`OVERLAY_CONFIG` in the following manner:

* For the experimental Thread 1.2 variant, set :file:`overlay-thread_1_2.conf`.
* For the minimal single protocol variant, set :file:`overlay-minimal_singleprotocol.conf`.
* For the minimal multiprotocol variant, set :file:`overlay-minimal_multiprotocol.conf`.

See :ref:`cmake_options` for instructions on how to add this option.
For more information about using configuration overlay files, see :ref:`zephyr:important-build-vars` in the Zephyr documentation.

Testing
=======

After building the sample and programming it to your development kit, test it by performing the following steps:

#. Turn on the development kit.
#. Set up the serial connection with the development kit.
   For more details, see :ref:`putty`.

   .. note::
        |thread_hwfc_enabled|

#. .. include:: /includes/thread_enable_network.txt
#. Invoke some of the OpenThread commands:

   a. Test the state of the Thread network with the ``ot state`` command.
      For example:

      .. code-block:: console

         uart:~$ ot state
         leader
         Done

   #. Get the Thread network name with the ``ot networkname`` command.
      For example:

      .. code-block:: console

         uart:~$ ot networkname
         ot_zephyr
         Done

   #. Get the IP addresses of the current thread network with the ``ot ipaddr`` command.
      For example:

      .. code-block:: console

         uart:~$ ot ipaddr
         fdde:ad00:beef:0:0:ff:fe00:800
         fdde:ad00:beef:0:3102:d00b:5cbe:a61
         fe80:0:0:0:8467:5746:a29f:1196
         Done

.. _ot_cli_sample_testing_multiple:

Testing with more boards
------------------------

If you are using more than one development kit for testing the CLI sample, you can also complete additional testing procedures.

.. note::
    The following testing procedures assume you are using two development kits.

Testing communication between boards
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To test communication between boards, complete the following steps:

#. Make sure both development kits are programmed with the CLI sample.
#. Turn on the developments kits.
#. Set up the serial connection with both development kits.
   For more details, see :ref:`putty`.

   .. note::
        |thread_hwfc_enabled|

#. .. include:: /includes/thread_enable_network.txt
#. Test communication between the boards with the following command:

   .. parsed-literal::
      :class: highlight

      ot ping *ip_address_of_the_first_board*

   For example:

   .. code-block:: console

      uart:~$ ot ping fdde:ad00:beef:0:3102:d00b:5cbe:a61
      16 bytes from fdde:ad00:beef:0:3102:d00b:5cbe:a61: icmp_seq=1 hlim=64 time=22ms

Testing diagnostic module
~~~~~~~~~~~~~~~~~~~~~~~~~

To test diagnostic commands, complete the following steps:

#. Make sure both development kits are programmed with the CLI sample.
#. Turn on the developments kits.
#. Set up the serial connection with both development kits.
   For more details, see :ref:`putty`.

   .. note::
        |thread_hwfc_enabled|.

#. Make sure that the diagnostic module is enabled and configured with proper radio channel and transmission power by running the following commands on both devices:

   .. code-block:: console

      uart:~$ ot diag start
      start diagnostics mode
      status 0x00
      Done
      uart:~$ ot diag channel 11
      set channel to 11
      status 0x00
      Done
      uart:~$ ot diag power 0
      set tx power to 0 dBm
      status 0x00
      Done

#. Transmit a fixed number of packets with the given length from one of the devices.
   For example, to transmit 20 packets that contain 100 B of random data, run the following command:

   .. code-block:: console

      uart:~$ ot diag send 20 100
      sending 0x14 packet(s), length 0x64
      status 0x00
      Done

#. Read the radio statistics on the other device by running the following command:

   .. code-block:: console

      uart:~$ ot diag stats
      received packets: 20
      sent packets: 0
      first received packet: rssi=-29, lqi=255
      last received packet: rssi=-30, lqi=255
      Done

.. _ot_cli_sample_testing_multiple_v12:

Testing Thread Specification v1.2 features
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To test the Thread Specification v1.2 features, complete the following steps:

#. Make sure both development kits are programmed with the CLI sample with the :ref:`Thread v1.2 extension <ot_cli_sample_thread_v12>` enabled.
#. Turn on the developments kits.
#. Set up the serial connection with both development kits.
   For more details, see :ref:`putty`.
#. .. include:: /includes/thread_enable_network.txt
#. Test the state of the Thread network with the ``ot state`` command to see which board is the leader:

   .. code-block:: console

      uart:~$ ot state
      leader
      Done

#. On the leader board, enable the Backbone Router function:

   .. code-block:: console

      uart:~$ ot bbr enable
      DIo:
       State changed! Flags: 0x02001000 Current role: 4
      I: State changed! Flags: 0x00000200 Current role: 4
      I: State changed! Flags: 0x02000001 Current role: 4

#. On the leader board, configure the Domain prefix:

   .. code-block:: console

      uart:~$ ot prefix add fd00:7d03:7d03:7d03::/64 prosD med
      Done
      uart:~$ ot netdataregister
      Done
      I: State changed! Flags: 0x00000200 Current role: 4
      I: State changed! Flags: 0x00001001 Current role: 4

#. On the router board, display the autoconfigured Domain Unicast Address and set another one manually:

   .. code-block:: console

      uart:~$ ot ipaddr
      fd00:7d03:7d03:7d03:ee2d:eed:4b59:2736
      fdde:ad00:beef:0:0:ff:fe00:c400
      fdde:ad00:beef:0:e0fc:dc28:1d12:8c2
      fe80:0:0:0:acbd:53bf:1461:a861
      uart:~$ ot dua iid 0004000300020001
      Io:
      State changed! Flags: 0x00000003 Current role: 3
      uart:~$ ot ipaddr
      fd00:7d03:7d03:7d03:4:3:2:1
      fdde:ad00:beef:0:0:ff:fe00:c400
      fdde:ad00:beef:0:e0fc:dc28:1d12:8c2
      fe80:0:0:0:acbd:53bf:1461:a861
      Done

#. On the router board, configure a multicast address with a scope greater than realm-local:

   .. code-block:: console

      uart:~$ ot ipmaddr add ff04::1
      Done
      : State changed! Flags: 0x00001000 Current role: 3
      uart:~$ ot ipmaddr
      ff04:0:0:0:0:0:0:1
      ff33:40:fdde:ad00:beef:0:0:1
      ff32:40:fdde:ad00:beef:0:0:1
      ff02:0:0:0:0:0:0:2
      ff03:0:0:0:0:0:0:2
      ff02:0:0:0:0:0:0:1
      ff03:0:0:0:0:0:0:1
      ff03:0:0:0:0:0:0:fc
      Done

   The router board will send an ``MLR.req`` message to the leader board (Backbone Router).
   This can be observed using the `nRF Sniffer for 802.15.4 based on nRF52840 with Wireshark`_.

   .. note::
        The DUA registration with the Backbone Router is not yet supported.

Dependencies
************

This sample uses the following Zephyr libraries:

* :ref:`zephyr:kernel_api`:

  * ``include/kernel.h``

* :ref:`zephyr:thread_protocol_interface`
