# Button Mask Connection Explained

**Button mask connection** is a distinctive feature of the CodexPad gamepad. It allows the host device to scan for and match a user-code-defined button combination (i.e., a "button mask") being held on the target gamepad, automatically connecting to the device with the strongest signal (highest RSSI) for fast and flexible pairing.

## How It Works

The core of button mask connection is a "handshake protocol" based on **physical actions** between the host device and the gamepad:

1. The host device pre-defines a "button mask" (Button Mask), which is a combination of one or more buttons (e.g., `Start + Cross`).
2. The host enters Bluetooth scanning mode, continuously listening for advertising packets from nearby discoverable gamepads.
3. The user **simultaneously presses and holds** the buttons corresponding to the mask on a gamepad without releasing them.
4. The host compares the scan results: only devices whose "currently pressed buttons" exactly match the "preset mask" are identified as connection targets.
5. When multiple qualifying devices are present, the host automatically selects the one with the strongest signal (highest RSSI) to connect to.
6. Once connected, the gamepad's Bluetooth indicator light changes from "slow blinking" to "solid on", and the user can release the buttons and operate the gamepad normally.

> **Fundamental Difference**: Address-based direct connection is "device-oriented" (bound to a specific Bluetooth device address), while button mask connection is "condition-oriented" (matching a certain button state). The former is precise but rigid, while the latter is flexible and secure.

## Design Intent and Advantages

### 1. Prevent Accidental Connections and Interference

When multiple connectable devices of the same type (e.g., multiple gamepads) are nearby, although you can achieve a precise connection using their unique Bluetooth device addresses, this usually requires "hardcoding" those addresses in the code. This approach binds the program to specific devices, lacking flexibility.

By requiring the target device to have a specific button combination pressed simultaneously when discovered, you essentially define a dynamic, condition-based connection rule. Your connection code does not need to bind to any device's Bluetooth device address; as long as a device satisfies this "handshake protocol" (holding the correct buttons), it will be connected. This effectively prevents the host from accidentally connecting to non-target devices among multiple options, while also enabling the convenience of **instant connection on press and the ability to switch devices at any time**.

### 2. Establish Exclusive Connection Conditions

You can regard this button mask as a simple "password" or "connection token." It creates an exclusive connection channel between your application and the device. Only devices that meet this specific physical interaction condition (pressing the designated buttons) can join, enhancing the intentionality and controllability of the connection.

### 3. Improve Code Flexibility and Support On-Demand Device Switching

Unlike hardcoding a specific device's Bluetooth device address in the code, the connection logic using a button mask is oriented toward "conditions" rather than "specific devices." This means your same connection code, without modification, can be used to connect to any gamepad that is in a discoverable state and correctly triggers the preset button condition. This brings two major benefits:

- **No need to bind to a specific device**: You don't need to specify a particular gamepad's Bluetooth device address in the code, nor maintain different connection configurations for different gamepads.
- **Instant connection on press, flexible switching**: In actual use, you can pick up another gamepad at any time. As long as it is powered on and the correct button combination is held, your program will automatically connect to it, achieving seamless switching between different gamepads.

## Notes on Button Masks

**The button mask is defined by the user in the host program**, meaning you write the corresponding mask value for the "button combination that needs to be pressed simultaneously." You can freely specify any **single button** or **combination of multiple buttons** according to your application scenario, such as `Start + Cross`, `Home + Square`, `Cross + Circle + Triangle`, etc.

> **⚠️ Important Note: Do not use the `Home` button alone as a mask.** Pressing and holding the `Home` button will trigger the gamepad to power off, causing connection interruption. If you must use the `Home` button, be sure to use it in a combination (e.g., `Home + Cross`).

For ease of understanding, the subsequent operation steps in this document uniformly use **`Start + Cross`** as the example mask:

## Operation Steps

The following uses "button mask set to `Start + Cross`" as an example to illustrate the complete connection process. If you have defined a different mask, please replace the buttons in the steps accordingly.

### Step 1: Host Device Sets Button Mask and Starts Scanning

In the host device's application, by calling the scan and connect interface provided by the CodexPad library, complete the following two tasks:

1. **Set the button mask**: Define the target button combination to match, which in this example is `Start + Cross` (Start button + Cross button).
2. **Start scan connection**: Call the corresponding connection function to put the host into Bluetooth scanning mode, to start listening for nearby discoverable gamepads.

After starting, the host enters a "waiting for match" state, continuously scanning and comparing the currently pressed buttons on nearby gamepads.

### Step 2: Power On the Gamepad and Enter Advertising (Ready to Connect) State

Power on the gamepad. After powering on, the gamepad automatically enters a discoverable Bluetooth **ready-to-connect state**, during which:

- The Bluetooth indicator light **blinks slowly** (approximately once per second);
- The gamepad continuously broadcasts its presence, waiting to be discovered by the host.

> If the indicator light is blinking rapidly, solid on, or off, please refer to the product manual to confirm the gamepad's current state, or restart it.

### Step 3: Hold the Corresponding Button Mask Until Connection Succeeds

While the gamepad is in the advertising state, **simultaneously press and hold** the buttons that exactly correspond to the preset mask in the code, and **do not release them**.

- In this example, the mask is `Start + Cross`, so you need to **simultaneously hold the Start button and the Cross button**.
- Maintain the press until the gamepad's **Bluetooth indicator light changes from slow blinking to solid on**, indicating that the host has successfully matched and established a connection.
- Only then can you release the buttons and operate the gamepad normally. The host will start receiving and outputting the gamepad's button and joystick data.

```text
Gamepad state changes:

  Power-on advertising  ──►  Hold Start + Cross  ──►  Bluetooth indicator solid on  ──►  Release buttons, normal operation
  (indicator slow blink)     (keep holding)            (connection successful)
```

> **Key Operation Points**
>
> - Buttons must be "pressed simultaneously" and held until the connection is established; releasing early will cause a mask mismatch, and the host will continue scanning for other devices.
> - If multiple gamepads satisfying the mask condition are nearby, the host will automatically connect to the one with the strongest signal (highest RSSI).

### Step 4: Reconnecting After Disconnection or When Changing Gamepads

Re-establishing a connection is required in the following two scenarios:

**Scenario A: Reconnection after abnormal disconnection**
**Scenario B: User needs to change to a new gamepad**

> **💡 Core Logic Explanation**
> Whether it is an abnormal disconnection or a proactive change, the button mask connection mechanism **does not require modifying the host program code**. The host program only needs to detect the disconnection and recall the corresponding connection function to enter the scanning state again.

#### Scenario A: Reconnection after abnormal disconnection

1. **Gamepad side**: Confirm that the original gamepad has been restarted and is in the advertising state (indicator light blinking slowly).
2. **Host side**: After detecting disconnection from the current gamepad, the host program recalls the connection function and enters the scanning state.
3. **Execute connection**: **Simultaneously press and hold** the preset button mask (e.g., `Start + Cross`) without releasing.
4. **Complete**: Wait for the indicator light to change from slow blinking to **solid on**, then release the buttons to resume normal operation.

#### Scenario B: Proactively changing to a new gamepad

1. **Disconnect the old device**: Proactively power off the currently connected gamepad to disconnect it from the host.
2. **Host side**: After detecting disconnection from the current gamepad, the host program recalls the connection function and enters the scanning state.
3. **Power on the new device**: Power on the new gamepad to put it into the advertising state (indicator light blinking slowly).
4. **Execute connection**: On the new gamepad, **simultaneously press and hold** the preset button mask (e.g., `Start + Cross`) without releasing.
5. **Complete**: Wait for the new gamepad's indicator light to change from slow blinking to **solid on**, then release the buttons. The host can then seamlessly connect to the new gamepad using the same program.

Thanks to the button mask connection mechanism, there is no need to modify the host program code. As long as the new gamepad holds the agreed-upon button combination, pairing and connection can be completed quickly, fully demonstrating the advantage of this feature for flexibly switching peripherals.

## Comparison with Address-Based Direct Connection

| Comparison Item | Address-Based Direct Connection | Button Mask Connection |
| :--- | :--- | :--- |
| Connection Method | Connect via fixed Bluetooth device address | Connect via matched button combination (condition) |
| Code Coupling | Requires hardcoding Bluetooth device address, bound to a specific device | No Bluetooth device address needed, condition-oriented, decoupled from specific devices |
| Multi-Device Environment | Prone to accidental connection to non-target devices | Precisely select target through physical handshake, avoiding accidental connections |
| Device Switching | Requires modifying the Bluetooth device address in code | Pick up any gamepad, hold the correct buttons to seamlessly switch |
| Applicable Scenarios | Fixed pairing, one-to-one long-term use | Shared gamepads, on-site flexible pairing, demonstrations and teaching |
| Prerequisites | Need to obtain and record the gamepad's Bluetooth device address in advance | Need to agree on a button mask in advance (can be regarded as a "connection password") |
