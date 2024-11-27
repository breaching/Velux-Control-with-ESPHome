# Velux Automation with ESP32 and KUX 100 Remote

This guide explains how to control your Velux windows using an **ESP32 microcontroller** connected to a **KUX 100 remote**. By following this DIY solution, you can automate your Velux blinds without purchasing the **VELUX ACTIVE with NETATMO hub**, which costs approximately **€129.99**.

---

## Materials and Costs

Here’s the list of materials needed for this project and their approximate costs:

| **Item**               | **Quantity** | **Estimated Cost** | **Details**                                               |
|-------------------------|--------------|---------------------|-----------------------------------------------------------|
| **ESP32 Microcontroller** | 1            | ~€4-€5           | ESP32 development board (e.g., ESP32-DevKitC or Wemos D1 Mini). |
| **2N3904 Transistors**    | 3            | ~€1-€2             | NPN transistors used to simulate button presses. Any similar NPN transistor (e.g., **2N2222**, **BC547**) can also be used. |
| **1 kΩ Resistors**       | 3            | ~€0.50             | For limiting current to the transistors.                 |
| **Wires and Connectors** | As needed    | ~€2                | Jumper wires or soldered connections.                    |
| **KUX 100 Remote Control** | 1            | €20-€40 (used)     | Available second-hand on platforms like eBay. I bought a spare one to avoid damaging my current one           |

**Total Estimated Cost**: €30–€50 (depending on the availability of the KUX 100 remote and other components).

---

## Why Choose This DIY Solution?

- **Cost-Effective**: A significantly cheaper alternative to the €129.99 Velux Active with Netatmo hub.
- **Customizable**: Full control over your Velux automation setup via ESPHome and Home Assistant.
- **Scalable**: Add more automation features or integrate with existing smart home systems.

---

## Features

- Control Velux window buttons (**UP**, **DOWN**, **STOP**) via ESP32.
- Integration with Home Assistant using ESPHome for easy automation.
- No need for expensive proprietary hubs.

---

## Instructions

Follow these steps to build and set up your Velux automation system using an **ESP32** and the **KUX 100 remote**.

---

### 1. **Prepare the Components**
#### Materials:
- ESP32 development board.
- 3 x NPN transistors (e.g., **2N3904**, **2N2222**, **BC547**).
- 3 x 1 kΩ resistors.
- Jumper wires or soldering equipment.
- A KUX 100 remote control.

---

### 2. **Assemble the Circuit**
Wire the ESP32 to the transistors and prepare the connections to the KUX 100 remote:



1. **Open the Remote:**
   ![](/photos/Untitled-2.jpg)
   - Remove the batteries as the remote will be powered by the ESP32.
   - Carefully push or pull the plastic clips holding the case together. Be gentle to avoid breaking the clips.
   - Slowly lift the PCB out of the case, paying attention to the metallic battery contacts:
   - Ensure you don’t break or tear these contacts, as they are still be needed for connections.
   - Turn the PCB around and peel off the tape or adhesive covering the buttons to reveal the contact pads.
   - The PCB is now ready for soldering and connecting to the ESP32.
![](/photos/Untitled-1.jpg)

3. **Connect the ESP32 to the transistors:**
   - **Base** of each transistor → Connect to an ESP32 GPIO (via a 1 kΩ resistor).
   - **Emitter** of each transistor → Connect to **GND** (shared with the KUX 100 remote).
   - **Collector** of each transistor → Connect to the corresponding pad on the KUX 100 remote:
     - Button UP → GPIO 17 → Transistor 1.
     - Button DOWN → GPIO 18 → Transistor 2.
     - Button STOP → GPIO 27 → Transistor 3.

4. **Connect the pads:**
   - Solder wires from the button pads (UP, DOWN, STOP) to the **collector** pins of their respective transistors.

5. **Share the ground and VCC:**
   - Connect the GND and 3.3V of the ESP32 to the GND and VCC of the KUX 100 remote.

---

I came up with a PCB like this (very ugly, ngl), but it's working. The ESP32 is actually mounted under the PCB, and I wired the **VCC** and **GND** to a 2-pin terminal block to power the entire setup.

![](/photos/2833AC95-E9AB-4DBA-80E0-8B1037A12F69.jpg)
---

### 3. **Flash the ESP32 with ESPHome**
1. Flash the code below:

   ```yaml
   esphome:
     name: velux_remote
     platform: ESP32
     board: esp32dev

   wifi:
     ssid: "Your_SSID"
     password: "Your_Password"

   captive_portal:

   logger:

   ota:

   output:
     - platform: gpio
       pin: 18
       id: button_up
     - platform: gpio
       pin: 27
       id: button_down
     - platform: gpio
       pin: 17
       id: button_stop
   
   # Switches pour contrôler les boutons
   switch:
     - platform: template
       name: "Velux UP"
       icon: "mdi:arrow-up"
       turn_on_action:
         - output.turn_on: button_up
         - delay: 200ms  # Maintient le bouton pressé pendant 200 ms
         - output.turn_off: button_up
   
     - platform: template
       name: "Velux DOWN"
       icon: "mdi:arrow-down"
       turn_on_action:
         - output.turn_on: button_down
         - delay: 200ms  # Maintient le bouton pressé pendant 200 ms
         - output.turn_off: button_down
   
     - platform: template
       name: "Velux STOP"
       icon: "mdi:stop"
       turn_on_action:
         - output.turn_on: button_stop
         - delay: 200ms  # Maintient le bouton pressé pendant 200 ms
         - output.turn_off: button_stop
   ```

## Troubleshooting
If you encounter any issues:
- **No response from the Velux remote:**
  - Ensure that your remote is paired to the blinds.
  - Check that the transistors are connected correctly and the solder joints are solid.
  - Ensure the ESP32 and KUX 100 remote share a **common ground**.
- **ESP32 not responding in Home Assistant:**
  - Verify the ESPHome configuration and network connectivity.
  - Check logs in the ESPHome dashboard for errors.

---

## To-Do
- **Design a Custom 3D-Printed Case**: Create a sleek and functional enclosure to house the ESP32, PCB, and wiring.
- **Improve the PCB Design**: Develop a more compact and organized PCB layout.


---

If you have any questions or improvements, feel free to open an issue or submit a pull request to this repository!
