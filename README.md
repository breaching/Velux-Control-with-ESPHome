# Velux Window Automation: ESP32 and KUX 100 Remote DIY Solution

## Overview

This guide provides a cost-effective method to automate Velux windows using an ESP32 microcontroller and KUX 100 remote, bypassing the expensive VELUX ACTIVE with NETATMO hub.

## 🛠 Bill of Materials

| Component | Quantity | Estimated Cost | Specifications |
|-----------|----------|----------------|----------------|
| ESP32 Microcontroller | 1 | €4-€5 | ESP32-DevKitC or Wemos D1 Mini |
| NPN Transistors (2N3904/2N2222/BC547) | 3 | €1-€2 | For button press simulation |
| 1 kΩ Resistors | 3 | €0.50 | Current limiting |
| Wires and Connectors | As needed | €2 | Jumper wires or soldered connections |
| KUX 100 Remote Control | 1 | €20-€40 | Second-hand, platform-compatible |

**Total Project Cost**: €30–€50

## 🚀 Key Advantages

- **Cost Reduction**: 75% cheaper than official VELUX hub
- **Full Customization**: Complete control via ESPHome and Home Assistant
- **Scalable**: Easy integration with existing smart home systems

## 🔧 Detailed Implementation Guide

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

### 3. ESPHome Configuration

```yaml
esphome:
  name: velux_remote
  platform: ESP32
  board: esp32dev

wifi:
  ssid: "Your_SSID"
  password: "Your_Password"

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
  
switch:
  - platform: template
    name: "Velux UP"
    turn_on_action:
      - output.turn_on: button_up
      - delay: 200ms  
      - output.turn_off: button_up
  
  - platform: template
    name: "Velux DOWN"
    turn_on_action:
      - output.turn_on: button_down
      - delay: 200ms  
      - output.turn_off: button_down
  
  - platform: template
    name: "Velux STOP"
    turn_on_action:
      - output.turn_on: button_stop
      - delay: 200ms
      - output.turn_off: button_stop
```


## 🛠 Troubleshooting

### Common Issues
- **No Remote Response**:
  - Confirm remote pairing with blinds
  - Verify transistor connections
  - Check ground connectivity

- **ESP32 Communication Problems**:
  - Validate ESPHome configuration
  - Check network settings
  - Review ESPHome dashboard logs

## 🔮 Future Improvements

- [ ] Design custom 3D-printed enclosure
- [ ] Develop more compact PCB layout

## 🤝 Contributing

Suggestions and improvements are welcome! Please open an issue or submit a pull request.
