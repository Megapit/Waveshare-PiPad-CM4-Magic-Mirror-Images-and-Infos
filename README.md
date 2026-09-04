# Waveshare 13.3inch PiPad C4 / Magic Mirror C4 -- Internal Hardware Notes

This repository documents the internal hardware of the **Waveshare
13.3inch PiPad C4** and the closely related **13.3inch Magic Mirror
C4**.

The official Waveshare documentation contains basic specifications and
setup information, but detailed high-resolution photographs of the
internal PCBs are difficult to find. The photos in this repository
document the boards, connectors, test pads and several undocumented or
poorly documented expansion possibilities.

> **Status:** Work in progress.\
> Information marked **Observed** is directly visible on the PCB/photos.
> Information marked **To verify** has not yet been electrically
> confirmed.

## Device overview

Officially documented features include:

-   Raspberry Pi Compute Module 4 (CM4)
-   13.3 inch IPS display
-   1920 × 1080 resolution
-   capacitive 10-point multitouch
-   Gigabit Ethernet
-   dual-band Wi-Fi and Bluetooth when supported by the installed CM4
-   two USB-A host ports
-   USB-C `USB SLAVE` port
-   microSD / TF-card slot
-   integrated speakers
-   MEMS microphone
-   3.5 mm audio output
-   RS232
-   RS485
-   12 V / 1 A power supply
-   75 × 75 mm VESA mounting pattern

Waveshare states that the PiPad C4 and Magic Mirror C4 are essentially
the same device internally; the main difference is the front panel: the
PiPad uses normal toughened glass, while the Magic Mirror uses one-way
mirror glass.

## Internal boards

The unit contains several PCBs.

### 1. Main carrier board -- `13.3inch Magic Mirror For CM4 (A)`

This is the main CM4 carrier board.

Observed components/interfaces:

-   Raspberry Pi CM4 board-to-board connectors
-   large passive CM4 heatsink
-   Gigabit Ethernet
-   2 × USB-A
-   USB-C `USB SLAVE`
-   microSD card slot
-   Wi-Fi antenna connection
-   internal `HDMI0` connection to the built-in display
-   internal audio connection
-   M.2 socket
-   HDMI1 solder/test pads
-   Camera solder/test pads
-   several GPIO/test pads
-   Ethernet test pads

### 2. Display / I/O board -- `13.3inch Magic Mirror For CM4 (B)`

Observed interfaces/components:

-   HDMI connection
-   audio connection
-   RS232 / RS485 terminal block
-   3.5 mm audio jack
-   power connector
-   OSD/control buttons:
    -   Power
    -   Up
    -   Left
    -   Menu
    -   Right
    -   Down
    -   Confirm
-   several DC/DC power-supply sections
-   display controller electronics

### 3. Touch controller board

The capacitive touch panel uses a separate small controller PCB
connected by FFC/FPC and cable to the rest of the system.

The touch panel itself is officially specified as **10-point capacitive
multitouch**.

------------------------------------------------------------------------

# Interesting undocumented / expansion features

## HDMI1 pads

**Observed**

The main carrier PCB exposes solder pads for the CM4's second HDMI
interface.

The bottom side of board A is explicitly labelled:

`HDMI1`

This is especially interesting because the internal 13.3 inch display
already uses the connection labelled:

`HDMI0`

This suggests the possibility of using the PiPad as a dual-display
computer:

``` text
CM4
 ├── HDMI0 -> internal 13.3" 1920x1080 display
 └── HDMI1 -> possible external second display
```

### To verify

-   exact HDMI1 pad pinout
-   whether all required HDMI signals are present
-   whether DDC/CEC/HPD and +5 V are exposed
-   whether any additional components are required
-   practical dual-display operation

Because HDMI uses high-speed differential pairs, any adapter PCB should
keep the connections short and impedance-controlled. Long hand-wired
connections are not recommended.

------------------------------------------------------------------------

# Camera pads

**Observed**

A second solder-pad area on the underside of the main carrier board is
explicitly labelled:

`CAMERA`

This is separate from the `HDMI1` pad area.

### To verify

-   whether this is CSI-2
-   exact pinout
-   supported camera interface
-   whether an adapter PCB already exists for the footprint

------------------------------------------------------------------------

# M.2 socket

**Observed**

The main carrier board contains an M.2-style socket next to the CM4.

The PCB also contains mounting positions labelled:

-   `2230`
-   `2242`

This strongly indicates support for M.2 modules with 30 mm or 42 mm
length.

This is particularly interesting because the Raspberry Pi CM4 exposes a
**PCIe 2.0 x1** interface.

### To verify

The following still needs electrical confirmation before inserting
hardware:

-   M.2 key type
-   whether the socket is actually connected to CM4 PCIe
-   supported module type
-   NVMe SSD compatibility
-   available power/current
-   PCIe enablement / device-tree configuration

**Do not assume NVMe compatibility solely from the physical M.2
connector until the pinout has been verified.**

If it is PCIe/NVMe capable, this would provide a much more robust
storage option than the microSD card.

------------------------------------------------------------------------

# Exposed test pads / GPIO

Several labelled pads are visible on board A.

Observed labels include:

``` text
GP8
GP23
GP24
GP25
```

and on the reverse side:

``` text
D2
GP10
GP9
GP11
GND
```

There are also Ethernet-related pads labelled:

``` text
ETH_IN
ETH_OUT
```

and additional pads labelled:

``` text
IP0
IP1
```

The exact function and electrical levels of all pads should be verified
before use.

------------------------------------------------------------------------

# Serial interfaces

The PiPad exposes both RS232 and RS485.

The Waveshare documentation specifies:

## RS232

Waveshare documents enabling UART3 with:

``` ini
dtoverlay=uart3
```

and identifies the device as:

``` text
/dev/ttyAMA1
```

## RS485

Waveshare documents use of the Raspberry Pi serial interface for RS485,
with the serial console disabled.

The physical terminal block on board B is labelled for RS232 and RS485.

This makes the PiPad potentially useful for industrial control,
automation, lighting-control experiments and other embedded applications
without requiring an external USB serial adapter.

------------------------------------------------------------------------

# USB

The carrier exposes:

-   2 × USB-A host ports
-   1 × USB-C `USB SLAVE`

The CM4 USB 2.0 interface may need to be explicitly configured as host
under Raspberry Pi OS.

Waveshare documents:

``` ini
dtoverlay=dwc2,dr_mode=host
```

The `USB SLAVE` connection can also be used when working with CM4 eMMC
variants.

------------------------------------------------------------------------

# Storage

Possible storage paths visible or known so far:

  Storage            Status
  ------------------ -----------------------------------------------
  microSD / TF       Confirmed
  CM4 eMMC           Supported with eMMC CM4
  USB mass storage   Possible through USB
  M.2                Physically present; interface still to verify
  NVMe               To verify

------------------------------------------------------------------------

# Display and touch

Internal display:

-   13.3 inch
-   IPS
-   1920 × 1080
-   178° viewing angle
-   capacitive touch
-   up to 10 simultaneous touch points
-   6H toughened glass on PiPad version

The internal display connection on the main PCB is labelled `HDMI0`.

The touch controller is implemented on a separate small PCB.

------------------------------------------------------------------------

# Audio

Observed / documented audio hardware includes:

-   integrated speakers
-   MEMS microphone
-   3.5 mm audio jack
-   internal audio ribbon/cable connection
-   dedicated audio circuitry on board B

------------------------------------------------------------------------

# Network

-   Gigabit Ethernet
-   Wi-Fi through wireless CM4 variants
-   Bluetooth through wireless CM4 variants
-   external/u.FL-style Wi-Fi antenna connection visible near the CM4

------------------------------------------------------------------------

# PCB markings

Known board markings:

``` text
13.3inch Magic Mirror For CM4 (A)
13.3inch Magic Mirror For CM4 (B)
```

The PiPad C4 apparently uses these Magic Mirror-labelled PCBs as well.
This agrees with Waveshare's documentation that PiPad C4 and Magic
Mirror C4 share the same underlying hardware and mainly differ in the
front glass.

------------------------------------------------------------------------

# Repository photo suggestions

A useful naming scheme for the photographs:

``` text
images/
├── board-a-front.jpg
├── board-a-back.jpg
├── board-b-front.jpg
├── board-b-back.jpg
└── touch-controller.jpg
```

Suggested photo descriptions:

  -----------------------------------------------------------------------
  File                                Description
  ----------------------------------- -----------------------------------
  `board-a-front.jpg`                 Main CM4 carrier, CM4/heatsink,
                                      USB, Ethernet, microSD, M.2

  `board-a-back.jpg`                  Reverse of main carrier; HDMI1 and
                                      Camera areas/test pads

  `board-b-front.jpg`                 Display/I/O controller PCB

  `board-b-back.jpg`                  OSD buttons, RS232/RS485, audio and
                                      power

  `touch-controller.jpg`              Separate capacitive-touch
                                      controller PCB
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# Known questions / TODO

-   [ ] Determine M.2 key and pinout
-   [ ] Verify PCIe connection from CM4 to M.2
-   [ ] Test NVMe SSD
-   [ ] Determine maximum supported M.2 power
-   [ ] Map HDMI1 pads
-   [ ] Design/test HDMI1 breakout PCB
-   [ ] Test dual-display operation
-   [ ] Map Camera pads
-   [ ] Identify Camera interface and connector pinout
-   [ ] Map exposed GPIO/test pads
-   [ ] Identify all major ICs on boards A and B
-   [ ] Document power rails and DC/DC converters
-   [ ] Check CM5 electrical compatibility
-   [ ] Check whether the onboard power supply can safely power a CM5
-   [ ] Create annotated PCB photographs

------------------------------------------------------------------------

# Safety / modification warning

The HDMI, camera and M.2 sections include high-speed interfaces.
Incorrect connections can damage the CM4, carrier board, display or
attached peripherals.

Before connecting hardware to undocumented pads or sockets:

1.  verify ground and supply pins,
2.  trace the relevant CM4 connector pins,
3.  check for shorts,
4.  confirm voltage levels,
5.  verify the interface with a schematic or continuity measurements
    where possible.

------------------------------------------------------------------------

# Official references

-   Waveshare -- 13.3inch PiPad C4\
    https://www.waveshare.com/13.3inch-pipad-c4.htm

-   Waveshare Wiki -- 13.3inch PiPad C4\
    https://www.waveshare.com/wiki/13.3inch_PiPad_C4

-   Waveshare Wiki -- 13.3inch Magic Mirror C4\
    https://www.waveshare.com/wiki/13.3inch_Magic_Mirror_C4

-   Raspberry Pi Compute Module 4 documentation\
    https://www.raspberrypi.com/documentation/computers/compute-module.html

------------------------------------------------------------------------

# Contributions

Additional information is welcome, especially:

-   schematics
-   M.2 / PCIe measurements
-   HDMI1 pin mapping
-   Camera pin mapping
-   IC identification
-   CM5 compatibility tests
-   high-resolution PCB photographs
-   working adapter-board designs

Please open an issue or pull request with measurements, photos or test
results.
