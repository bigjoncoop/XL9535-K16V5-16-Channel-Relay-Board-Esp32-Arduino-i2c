# XL9535-K16V5-16-Channel-Relay-Board-Esp32-Arduino-i2c
Complete working code and documentation for controlling XL9535-K16V5 16-channel I2C relay boards with ESP32, Arduino, and other microcontrollers. Includes multi-board daisy-chain setup, power configuration, and beginner-friendly AI prompt for code generation.

# XL9535-K16V5 16-Channel I2C Relay Module - Complete Guide

## 🎯 What is this?

This repository provides **working code and comprehensive documentation** for the **XL9535-K16V5 16-channel relay module** - an I2C-controlled relay board that's often difficult to get working due to limited documentation. This guide will help you control 1-128 relays using just 2 I2C pins!

## 🔍 SEO Keywords
`XL9535-K16V5`, `16 channel relay`, `I2C relay module`, `ESP32 relay control`, `Arduino relay board`, `PCAL9535A`, `XL9535`, `multi-relay control`, `daisy chain relay`, `I2C GPIO expander relay`

---

## 📋 Board Specifications

- **Model**: XL9535-K16V5 16-Channel Relay Module
- **Communication**: I2C (IIC) with opto-isolated interface
- **Relays**: 16 channels per board
- **I2C Chip**: Dual XL9535/PCAL9535A GPIO expanders
- **Base I2C Address**: 0x20 (configurable via A0, A1, A2 jumpers)
- **Control Voltage**: Compatible with 3.3V or 5V logic
- **Load Capacity**: 10A @ 250VAC (resistive), 7A (inductive)
- **Power Requirements**: 
  - Logic: 3.3V or 5V (VIN)
  - Relay Coils: 5V @ ~100mA per relay
- **Expandability**: Up to 8 boards (128 relays) on one I2C bus

---

## ⚡ Power Configuration

### For 5V Microcontrollers (Arduino Uno, Mega, etc.)
1. **Keep J1 jumper installed**
2. Connect 5V to VIN
3. Connect GND to GND
4. I2C operates at 5V logic levels

### For 3.3V Microcontrollers (ESP32, ESP8266, Raspberry Pi)
1. **REMOVE J1 jumper** ⚠️ IMPORTANT!
2. Connect 3.3V to VIN
3. Provide separate 5V power to one of:
   - DC barrel jack (center positive)
   - Type-C USB port
   - KF301 screw terminals (5V terminal)
4. Connect GND to both microcontroller and 5V power source
5. I2C operates at 3.3V logic levels

**Warning**: Never connect 5V to VIN with J1 jumper installed when using 3.3V devices - this will damage your microcontroller!

---

## 🔌 Wiring Diagram

### Single Board Connection (ESP32 Example)
```
ESP32          XL9535-K16V5
------         -------------
3.3V    --->   VIN (J1 removed!)
GND     --->   GND
GPIO21  --->   SDA
GPIO22  --->   SCL

5V Power Source (separate)
USB/DC Jack ---> 5V input on relay board
GND ---------->  GND (common ground)
```

### Multi-Board Daisy Chain (up to 8 boards)
```
ESP32 --> Board 1 (0x20) --> Board 2 (0x21) --> Board 3 (0x22) ...
          |                  |                  |
          SDA/SCL           SDA/SCL            SDA/SCL
          (parallel)        (parallel)         (parallel)
```

**I2C Bus Connections (All Boards in Parallel)**:
- Connect all SDA pins together
- Connect all SCL pins together
- Connect all GND pins together
- Each board needs its own 5V power supply connection

---

## 🎚️ I2C Address Configuration

Each board has three address jumpers: **A0, A1, A2**

| A2 | A1 | A0 | I2C Address | Hex  |
|----|----|----|-------------|------|
| 0  | 0  | 0  | 32          | 0x20 |
| 0  | 0  | 1  | 33          | 0x21 |
| 0  | 1  | 0  | 34          | 0x22 |
| 0  | 1  | 1  | 35          | 0x23 |
| 1  | 0  | 0  | 36          | 0x24 |
| 1  | 0  | 1  | 37          | 0x25 |
| 1  | 1  | 0  | 38          | 0x26 |
| 1  | 1  | 1  | 39          | 0x27 |

- **0** = Jumper removed/open
- **1** = Jumper installed/closed

**Default**: All jumpers open = 0x20

---

## 🧠 How the Board Works

### Internal Architecture
Each XL9535-K16V5 board contains **TWO** XL9535 GPIO expander chips sharing the same I2C address:

- **Controller A**: Controls relays 0-7 (P0.0 to P0.7)
- **Controller B**: Controls relays 8-15 (P1.0 to P1.7)

### Register Map
| Register | Function | Controls |
|----------|----------|----------|
| 0x06 | Configuration Port 0 | Set Controller A as input/output |
| 0x07 | Configuration Port 1 | Set Controller B as input/output |
| 0x02 | Output Port 0 | Control relays 0-7 |
| 0x03 | Output Port 1 | Control relays 8-15 |

### Initialization Sequence
1. Write `0x00` to register `0x06` (set Controller A pins as outputs)
2. Write `0x00` to register `0x07` (set Controller B pins as outputs)
3. Now you can control relays by writing to registers `0x02` and `0x03`

### Relay Control
- Each bit in the output register controls one relay
- **Bit = 1**: Relay ON (energized)
- **Bit = 0**: Relay OFF (de-energized)

Example: To turn on relays 0, 2, and 5 on Controller A:
```
Binary: 0b00100101 = 0x25 = 37 decimal
Write 0x25 to register 0x02
```

---

## 📚 Working Code Examples

### See the `examples/` folder for:
- ✅ ESP32 basic control
- ✅ Arduino Uno/Mega
- ✅ Multiple board control
- ✅ Web server control interface
- ✅ MQTT integration

---

## 🤖 AI Code Generation Prompt

**Copy and paste this into ChatGPT, Claude, or any AI assistant to generate custom code:**

```
I need you to write code for controlling an XL9535-K16V5 16-channel I2C relay module with my [SPECIFY: ESP32/Arduino/etc].

CRITICAL BOARD ARCHITECTURE:
- The board has TWO XL9535 GPIO expander chips at the SAME I2C address (default 0x20)
- Controller A controls relays 0-7 using registers 0x06 (config) and 0x02 (output)
- Controller B controls relays 8-15 using registers 0x07 (config) and 0x03 (output)

INITIALIZATION REQUIRED:
1. Send 0x06, then 0x00 to configure Controller A as outputs
2. Send 0x07, then 0x00 to configure Controller B as outputs

RELAY CONTROL:
- Write to register 0x02 to control relays 0-7
- Write to register 0x03 to control relays 8-15
- Bit value 1 = relay ON, 0 = relay OFF
- Use binary patterns like 0b00001111 or hex like 0x0F

MY HARDWARE SETUP:
- Microcontroller: [SPECIFY]
- I2C pins: SDA = [PIN], SCL = [PIN]
- Power: [3.3V or 5V logic]
- Number of relay boards: [1-8]
- I2C addresses: [0x20, 0x21, etc.]

FUNCTIONALITY NEEDED:
[Describe what you want the relays to do, for example:]
- Control individual relays on/off
- Turn all relays on/off at once
- Create timed sequences
- Web interface control
- MQTT/WiFi integration
- Read sensor and control relays based on conditions

ADDITIONAL REQUIREMENTS:
[Any specific features you need]

Please provide:
1. Complete working code with comments
2. Explanation of how it works
3. Wiring instructions
4. Any libraries needed
```

---

## 🔧 Troubleshooting

### Problem: No relays respond
**Solutions:**
1. Verify I2C address with an I2C scanner sketch
2. Check that initialization sequence was sent (registers 0x06 and 0x07)
3. Ensure proper power supply (5V for relay coils)
4. Check J1 jumper configuration matches your logic voltage
5. Verify SDA/SCL connections aren't swapped

### Problem: Only some relays work
**Solutions:**
1. Ensure BOTH controllers are initialized (0x06 AND 0x07)
2. Check that you're writing to correct register (0x02 for relays 0-7, 0x03 for 8-15)
3. Verify adequate 5V power supply current (1.6A for all 16 relays on)

### Problem: Relays click but don't switch load
**Solutions:**
1. Check relay load connections (COM, NO, NC terminals)
2. Verify load voltage/current within relay ratings
3. Test relay with multimeter to confirm contacts closing
4. Some loads (especially LED bulbs) may need minimum current

### Problem: Multiple boards not working
**Solutions:**
1. Verify each board has unique I2C address (check A0/A1/A2 jumpers)
2. Check all boards share common GND
3. For long cables or many boards, add 4.7kΩ pull-up resistors on SDA/SCL
4. Ensure adequate 5V power for all boards

### Problem: ESP32 crashes or reboots
**Solutions:**
1. Separate 5V relay power from ESP32 power supply
2. Add 100µF capacitor near relay board 5V input
3. Keep I2C cables short (< 1 meter) or add pull-ups
4. Reduce I2C clock speed: `Wire.setClock(100000);`

---

## 📖 Compatible Libraries

While you can use raw I2C commands (as shown in examples), these libraries also work:

- **PCAL9535A Arduino Library**: https://github.com/chrissbarr/PCAL9535A-Arduino-Library
- **Adafruit_MCP23017** (similar API, needs register changes)
- **PCA95x5 Library** (community reported working)

Note: Most examples here use raw `Wire` library for maximum compatibility and understanding.

---

## 🌐 Multi-Board Setup Example

To control 32 relays (2 boards):

```cpp
#define BOARD1_ADDR 0x20  // A0=0, A1=0, A2=0
#define BOARD2_ADDR 0x21  // A0=1, A1=0, A2=0

void setup() {
  Wire.begin();
  
  // Initialize both boards
  initBoard(BOARD1_ADDR);
  initBoard(BOARD2_ADDR);
}

void initBoard(uint8_t addr) {
  // Controller A
  Wire.beginTransmission(addr);
  Wire.write(0x06);
  Wire.write(0x00);
  Wire.endTransmission();
  
  // Controller B
  Wire.beginTransmission(addr);
  Wire.write(0x07);
  Wire.write(0x00);
  Wire.endTransmission();
}

void setRelay(uint8_t boardAddr, uint8_t relayNum, bool state) {
  // Implementation here...
}
```

---

## 📐 Relay Terminal Connections

Each relay has 3 terminals:

- **COM** (Common): Connect your power source or load
- **NO** (Normally Open): Closed when relay is energized (ON)
- **NC** (Normally Closed): Open when relay is energized (ON)

### Example: Controlling a 12V LED strip
```
12V+ -----> COM
            NO -----> LED Strip +
            NC -----> (unused)
LED Strip - ------> 12V-
```

---

## ⚠️ Safety Notes

1. **DO NOT exceed relay ratings**: 10A resistive, 7A inductive
2. **Use appropriate fuses** for your loads
3. **High voltage warning**: AC mains can be lethal - use qualified electrician
4. **Inductive loads**: Add flyback diodes/snubbers for motors/solenoids
5. **Heat dissipation**: Space boards adequately if switching high currents
6. **Opto-isolation**: Board provides I2C isolation, but always follow safety practices

---

## 🤝 Contributing

Found a better method? Have a different use case? Contributions welcome!

1. Fork the repository
2. Create your feature branch
3. Test thoroughly
4. Submit a pull request

---

## 📄 License

MIT License - Feel free to use in commercial and personal projects

---

## 🙏 Credits

Information compiled from:
- User reviews and community forum posts
- Experimentation and testing
- Original product documentation (limited)

Special thanks to the community members who shared their working configurations!

---

## 📧 Support

- **Issues**: Use GitHub Issues for bug reports
- **Discussions**: Use GitHub Discussions for questions
- **Pull Requests**: Always welcome!

---

## 🔗 Related Projects

- ESP32 Home Automation
- Arduino Relay Controllers
- I2C GPIO Expanders
- Multi-relay control systems

---

**Last Updated**: [Date]
**Tested With**: ESP32, Arduino Uno, Arduino Mega, ESP8266
