# uLisp for Serpente boards

The Arturo182 Serpente boards are based on the Microchip ATSAMD21E, an ARM Cortex M0+ CPU with a 48 MHz clock. There are three variants which differ only in the type of USB connector provided.

For full information about the Serpente boards see: [Serpente Documentation](https://serpente.solder.party).

#### Installing the Serpente core

To enable the boards to be used with uLisp, and upload uLisp from the Arduino IDE, I've created an Arduino Core for the Serpente boards which you need to install as follows before proceeding:

* Download the folder **SerpenteCore**.
* Move it into your **Arduino/hardware** folder.

When you restart the Arduino IDE there should be a new heading **Arturo182 Serpente Boards** on the **Boards** submenu with a single **Serpente** entry.

Now upload the file **ulisp-serpente.ino**, and when you open the Arduino **Serial Monitor** you should see the uLisp prompt.

#### Description

Each board provides 256 KB flash and 32 KB RAM, and also include a separate 4 MB SPI DataFlash chip which is used by uLisp to allow you to save the Lisp image using (save-image).

These boards all have similar performance to the Arduino Zero and other Adafruit M0 boards when running uLisp; for example, they run the Tak benchmark in 14 secs; see Performance.

Install the ARM version of uLisp for use with these boards.

#### I/O pins

Apart from the power pins the boards provide six multi-purpose pins:

| Label | Functions |
| ----- | --------- |
| D0 | Digital I/O, analogue input, PWM, MOSI. |
| D1 | Digital I/O, analogue input, PWM, SCK. |
| D2 | Digital I/O, analogue input, PWM, MISO. |
| D3 | Digital I/O, analogue input, PWM. |
| D4 | Digital I/O, analogue input, PWM, SDA, TX. |
| D5 | Digital I/O, analogue input, PWM, SCL, RX. |

#### LED

The Serpente has an RGB LED connected to the following Arduino pin numbers:

| Pin | Functions |
| --- | --------- |
| D6 | Red. |
| D7 | Green. |
| D8 | Blue. |

It's currently not quite working correctly; hopefully this will be fixed in a future release.

You can flash it red with the following program:

````text
(defun blink (&optional x)
  (pinmode 6 t)
  (digitalwrite 6 x)
  (delay 1000)
  (blink (not x)))
````
Run it by typing:

    (blink)

Exit from the program by entering ~.
