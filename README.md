# MG24 Antenna Diversity Adustments for EFR32MG24 TIS Testing

This is an extension of the Silicon Labs StandardizedRfTesting firmware application which adds some register adjustments to optimize the RX antenna diversity behavior for TIS chamber testing for the Silicon Labs EFR32MG24 device.

# Background 

Here are a few background articles:

[How To Test and Estimate TIS/TRP](https://community.silabs.com/s/article/how-to-test-and-estimate-tis-trp?language=en_US)

[How To Build and Use the StandardizedRfTesting Sample in the EmberZNet SDK](https://community.silabs.com/s/article/how-to-build-and-use-the-standardizedrftesting-sample-in-emberznet-sdk-x?language=en_US)

The way the TIS chamber test (like the popular EMQuest test suite from ETS Lindgren) is typically run relies on the receiver always selecting the strongest antenna to receive a packet. The reason for this is that the antenna pattern is established for all DUT orientations other than the strongest direction (boresight/EIS) using RSSI. The RSSI is summed for each DUT orientation and then calculated relative to the boresight/EIS, on which a full received sensitivity test is performed. When the weaker antenna is used to receive a packet during the test, the total RSSI for that position is lowered and this has the effect of degrading the calculated TIS performance for that orientation. We have found in our testing that for higher power levels (generally above -60 dBm), the default behavior of the MG24 in RX antenna diversity mode is to sometimes select the weaker signal. In the field, this generally doesn't matter since the packet will be received just fine at the higher power level, even from the weaker antenna. But this is a problem for the TIS chamber test, so we need to apply a few low-level register adjustments when in RX diversity mode in order to improve this behavior. Note that the signal levels in the TIS chamber are typically low (close to the RX sensitivity level), but the EMQuest chamber test does perform an RSSI linearization step where it sends packets of varying amplitudes (including above -60 dBm) to confirm that the RSSI response on the DUT is linear. These adjustments are required to successfully respond to the RSSI linearization test performed during the EMQuest RSSI linearization step. 

# Getting Started

This repo contains a copy of app.c from the StandardizedRfTesting example from the Silicon Labs EmberZNet SDK which has been modified to automatically apply the adjustment on startup when the default configuration (in the “Platform->Radio->RAIL Utility, Antenna Diversity Configuration” component) is set to RX antenna diversity. It can also be applied at run time via command line ("custom silabs applyAntDivAdjust") if there direct serial CLI access to the DUT. The adjustment will not be applied if the device isn't currently configured for antenna diversity on RX.

To use:
1. Create a StandardizedRfTesting firmware example project for your target hardware. 
2. Configure antenna diversity via the “Platform->Radio->RAIL Utility, Antenna Diversity Configuration” component in the project slcp file.
2. Replace or merge the app.c with the applicable app.c from this repo. 

# Supported SDK Versions

The Simplicity SDK version has been tested in Simplicity SDK Suite v2024.06.0.

The Gecko SDK version (tested in GSDK 4.4.3) will be supplied soon.

## Authors

* **Kris Young** - *Initial work* - [Kris Young](https://github.com/kryoung-silabs) <<kris.young@silabs.com>>

## License

This AS-IS example project is licensed under Zlib by Silicon Laboratories Inc. See the [LICENSE.md](LICENSE.md) file for details
