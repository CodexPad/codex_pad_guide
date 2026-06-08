# Important Notes

## Protocol Description

The  **CodexPad** series controllers communicate using a **standard Bluetooth Low Energy (BLE) protocol**, which is fundamentally different from the common **BLE HID** protocol.

- **Protocol type**: This product uses a custom standard BLE protocol optimized for embedded development, not a plug-and-play BLE HID protocol.

- **Connection and usage**: This means that your computer or mobile operating system (such as Windows, macOS, Android, iOS, Linux) **will not recognize the gamepad as a standard controller or input device** after scanning and successfully connecting to it. Therefore, it cannot be used directly in any game or application.

- **Correct usage**: This product is designed from the ground up as **a programmable input module**. You must write your own host-side code to connect to the gamepad, read its data, and implement the control logic you need.

  - **Primary supported mode**: We provide comprehensive libraries and example code for mainstream hardware platforms. This is the recommended direction and the one that receives official technical support.

  - **Advanced usage mode**: Technical developers can also write host-side code on a **computer (e.g., using Python, Node.js, etc.) or mobile phone (e.g., using Android Studio, Swift, etc.)** to connect to and control the gamepad. This is an advanced usage for specific projects, and **we do not provide official technical support, libraries, or examples for this mode**. Regarding the underlying BLE GATT communication characteristics, we will assess market demand to decide whether to provide detailed documentation in the future.

  - **Theoretical compatibility statement**: From a technical principle standpoint, this controller **theoretically supports all hardware platforms with Bluetooth Low Energy (BLE) master device capabilities**. If you need to use it on a platform not explicitly supported by us (such as other models of microcontrollers, single-board computers, or specific devices), you will need to **develop your own host-side driver based on our open communication protocol**, or follow our future updates and wait for official support.

> **💡 Please be aware**: This product is a development tool designed for **programmable embedded projects**. If you need a plug-and-play solution for your computer or phone, this product will not meet your needs. If you are a capable developer who wishes to integrate this gamepad on a PC or mobile device, you will need to research its BLE communication protocol on your own.
