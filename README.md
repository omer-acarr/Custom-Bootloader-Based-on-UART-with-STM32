# STM32 Custom UART Bootloader (IAP)

This project is a Bootloader software I developed for STM32F4 series microcontrollers, enabling software updates (In-Application Programming - IAP) via the serial port (UART) without the need for an external programmer (ST-Link/J-Link).

I designed this architecture to gain a deep understanding of “Memory Management” and “Bare-metal” programming principles, which are among the most critical competencies in embedded systems.

## 🎯 Project Purpose and Motivation
In embedded software engineering, it is a vital requirement that a device operating in the field can be updated without opening its cover. In this project, I went beyond standard HAL libraries and focused on **Linker Script** manipulation, **Flash Memory** sector management, and direct control of the **Processor Core (ARM Cortex-M4)** registers.

My goal is not only to write working code, but to control the entire “Boot” process from the moment the processor “Resets” until it transitions to the main application.

## ⚙️ Technical Details and Architecture
The project works by dividing the microcontroller's Flash memory into two main sections:

1.  **Bootloader Section (Sector 0):** This is the first code that runs when the device is powered on. It waits for a certain period of time (timeout) or a trigger (button/command). If there is an update request, it listens to the UART line.
2.  **Application Section (Sector 1+):** This is where the user's main code runs.

### Key Technical Competencies
* **Flash Memory Management:** Sector-based erasure and 4-byte/word-based writing of flash memory were performed safely by checking hardware error flags.
* **Linker Script Editing:** The memory map in the `.ld` file was reorganized to prevent conflicts between the Bootloader and Application code.
* **Jump to Application (Branching):** When the Bootloader completed its task, the processor's Program Counter (PC) and Stack Pointer (MSP) addresses were directed to the start address of the main application using the `Function Pointer`.
* **Vector Table Relocation:** The offset value of the interrupt vector table (Interrupt Vector Table) was dynamically shifted (SCB->VTOR) to allow the main application to run.

## 🛠 Technologies and Tools Used
* **Hardware:** STM32F4 Discovery Kit (ARM Cortex-M4)
* **Software Language:** Embedded C
* **IDE:** STM32CubeIDE
* **Communication:** UART (Universal Asynchronous Receiver-Transmitter)
* **Test Tools:** Tera Term / RealTerm (for binary data transfer)

## 🚀 How Does It Work?
1.  When the device starts up, the Bootloader kicks in.
2.  If the user presses the button or a specific “Handshake” byte arrives via UART, it enters **Update Mode**.
3.  The new software `.bin` file sent from the computer is received in packets.
4.  The incoming data is written to the relevant sectors of the Flash memory after CRC checking.
5.  When the write operation is complete, the system is reset or jumps directly to the main application.

## 📈 Future Developments (To-Do)
* Create a secure boot infrastructure by adding AES-128 encryption to data transfer.
* Integrate an advanced CRC-32 check to verify the integrity of the software.
* Add USB (DFU Class) support to the communication interface.

---
*This project was developed by Ömer Faruk Acar to understand the low-level working logic of embedded systems and apply professional firmware update standards.*
