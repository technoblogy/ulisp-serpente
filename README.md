Â# uLisp for Serpente boards

This is a version of uLisp 3.0 designed to work with the Arturo182 Serpente boards:

![image](http://www.ulisp.com/pictures/3j/Serpente-small.jpg)

It's based on the ARM version of uLisp. For more information about uLisp see: [uLisp - Lisp for microcontrollers](http://www.ulisp.com/show?3M).

I've also created an Arduino Core that is needed by uLisp. You could also use this on its own to program the Serpente boards in C from the Arduino IDE; see: [An Arduino core for the Serpente boards](https://github.com/technoblogy/serpente-core).

For full information about the Serpente boards see: [Serpente Documentation](https://serpente.solder.party).

To buy the boards see: [Serpente - A Tiny CircuitPython Prototyping Board](https://www.tindie.com/products/arturo182/serpente-a-tiny-circuitpython-prototyping-board/) on Tindie.

#### Installing the Serpente core

To enable the boards to be used with uLisp, and upload uLisp from the Arduino IDE, first install the Arduino Core as follows:

* Download the Serpente core from: https://github.com/technoblogy/serpente-core.

* Move it into your **Arduino/hardware** folder.

When you restart the Arduino IDE there should be a new heading **Arturo182 Serpente Boards** on the **Boards** submenu with a single **Serpente** entry.

Now upload the file **ulisp-serpente.ino**. NOTE: this will overwrite CircuitPython. When you open the Arduino **Serial Monitor** you should then see the uLisp prompt.

#### Description

These boards are based on the Microchip ATSAMD21E, an ARM Cortex M0+ CPU with a 48 MHz clock. There are three variants which differ only in the type of USB connector provided. Each board provides 256 KB flash and 32 KB RAM, and also include a separate 4 MB SPI DataFlash chip which is used by uLisp to allow you to save the Lisp image using (save-image).

These boards all have similar performance to the Arduino Zero and Adafruit M0 boards when running uLisp; for example, they run the Tak benchmark in 14 secs; see [Performance](http://www.ulisp.com/show?3M#performance).

#### I/O pins

Apart from the power pins the boards provide six multi-purpose pins:

| Label | Functions |
| ----- | --------- |
| D0 | Digital I/O, analogue input, analogue output (PWM), MOSI. |
| D1 | Digital I/O, analogue input, analogue output (PWM), SCK. |
| D2 | Digital I/O, analogue input, analogue output (PWM), MISO. |
| D3 | Digital I/O, analogue input, analogue output (PWM). |
| D4 | Digital I/O, analogue input, analogue output (PWM), SDA, TX. |
| D5 | Digital I/O, analogue input, analogue output (PWM), SCL, RX. |

The following sections give some simple examples of using these functions from uLisp. In each case you just need to type the commands into the Arduino IDE's Serial Monitor.

#### LED

The Serpente has an RGB LED connected to the following Arduino pin numbers:

| Pin | LED |
| --- | --------- |
| D6 | Red |
| D7 | Green |
| D8 | Blue |

For example, to flash an LED define:

````text
(defun blink (led &optional x)
  (pinmode led t)
  (digitalwrite led x)
  (delay 1000)
  (blink led (not x)))
````
Flash the red LED by typing:

    (blink 6)

The LED pins can also be used as analogue pins, so you can pulsate an LED slowly on and off with the program:
````text
(defun pulse (led)
  (let (down)
    (loop
     (dotimes (x 256)
       (delay 5)
       (analogwrite led (if down (- 255 x) x)))
     (setq down (not down)))))
````
Pulsate the green LED by typing:

    (pulse 7)

Exit from either program by entering ~.

#### Digital output

To define a pin for digital output use the function **pinmode**. For example, to make D0 an output:

    (pinmode 0 t)

You can then control the pin using **digitalwrite**. For example, the following function **signal** generates a 10 Hz square wave on a specified pin:
````text
(defun signal (pin)
  (pinmode pin t)
  (loop
   (digitalwrite pin t)
   (delay 50)
   (digitalwrite pin nil)
   (delay 50)))
````
Call it by entering, for example:

    (signal 0)

#### Analogue input

To read the analogue voltage on a pin use the function analogread. This returns a 12-bit number, from 0 to 4095.

For example, if you have an ADXL335 3-Axis Accelerometer connected to pins D0, D1, and D2 you can display the X, Y, and Z outputs as a list of three numbers with the following function:
````text
(defun xyz ()
  (list (analogread 0)
        (analogread 1)
        (analogread 2)))
````

#### I2C

To communicate with an I2C device use the uLisp command **with-i2c**. This creates an I2C stream variable that you can provide as an argument to any of the standard input or output functions. For example, if you had an I2C alphanumeric display with address 123 you could write to it with the command:
````text
(with-i2c (123 str)
   (print "HELLO" str))
````
You can write a simple port scanner that prints the address of each of the I2C devices on the bus:
````text
(defun scn ()
  (dotimes (p 127)
    (with-i2c (str p)
      (when str (print p)))))
````
#### SPI

To communicate with an SPI device use the uLisp command **with-spi**. This creates an SPI stream variable that you can provide as an argument to the input or output functions.

For example, to write 3 bytes to an SPI slave device using pin D3 as the SPI select pin:
````text
(with-spi (str 3)
  (write-byte 0 str)
  (write-byte 1 str)
  (write-byte 2 str))
````
#### Serial

In a similar way, to communicate with a serial device use the uLisp command **with-serial**. This creates a serial stream variable that you can provide as an argument to the input or output functions.

For example, to print the serial output from a 9600 baud GPS module connected to Serial1 you just need to write:
````text
(with-serial (str 1 96)
  (loop
    (print (read-line str))))
````
