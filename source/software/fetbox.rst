FETbox Serial Control
#####################

Convenience functions for serial control of the *PlateFlo FETbox*

1. Key Features
^^^^^^^^^^^^^^^
* Simple, high-level hardware control through USB serial communication
* Hit-and-hold capability for power-efficient solenoid operation
* Full Arduino output pin control, including PWM functionality
* Full Arduino digital and analog pin reading functionality
* Asynchronous serial I/O for non-blocking operation via the :mod:`.serial_io`
  module
* Logging via the base Python ``logging`` module

2. Quick Start
^^^^^^^^^^^^^^^

Installation
==================
The ``plateflo`` package is hosted on `PyPI
<https://pypi.org/project/plateflo>`_ and can be installed using ``pip``:

.. code:: bash

   pip install plateflo

*or*

.. code:: bash

   python -m pip install plateflo

More detailed instructions on installing Python modules can be found `here
<https://docs.python.org/3/installing/index.html>`_.

Setup
==============

Instantiate a :class:`.FETbox` object:

.. code-block:: python

   >>> from plateflo import fetbox 
   >>> 
   >>> # Connects to a FETbox on serial port 'COM3'
   >>> my_fetbox = FETbox(port = "COM3")

This creates a :class:`.FETbox` object and opens a serial connection with the
device.

.. Note::
   Device must be connected when the FETbox object is instantiated.

You can automatically instantiate all connected FETboxes with
:func:`.auto_connect_fetbox()`:

.. code-block:: python

   >>> # returns a dictionary of FETbox devices, keyed by their IDs
   >>> my_fetboxes = auto_connect_fetbox()
   
   >>> # run a motor on MOSFET output channel 1 of device ID 1
   >>> my_fetboxes[1].enable_chan(1)

.. Tip::
   When exiting from your program, call :meth:`my_fetbox.kill()
   <.fetbox.kill()>` to close the serial port and end all FETbox-associated
   threads running in the
   background.


Alternatively, discover connected devices using the :func:`.scan_for_fetbox()`
function,

.. code-block:: python

   >>> # Scan systems serial ports for FETbox(es)
   >>> fetboxes = scan_for_fetbox()

   >>> # One device found:
   >>> print(fetboxes)
   >>> [{'port':'COM3', 'id':0}, {'port':'COM4', 'id':1}]
   >>> 
   >>> # Multiple devices found:
   >>> print(fetboxes)
   >>> [{'port':'COM3', 'id':0}, {'port':'COM4', 'id':1}]

   >>> # No devices found:
   >>> print(fetboxes)
   >>> []

then instantiate using the result:

.. code-block:: python

   >>> my_fetbox = FETbox(port = fetboxes[0]['port'])

3. Usage
^^^^^^^^^

MOSFET Output Channel Control
==============================

There are four built-in methods for control of the FETbox's five MOSFET output
channels:

:Enable: :meth:`enable_chan(chan) <.enable_chan()>`
:Disable: :meth:`disable_chan(chan) <.disable_chan()>`
:PWM: :meth:`pwm_chan(chan, pwm) <.pwm_chan()>`
:Hit-and-Hold: :meth:`hit_hold_chan(chan, duty) <.hit_hold_chan()>`

:meth:`.enable_chan()` and :meth:`.disable_chan()` simply set the specified 
channel's (``chan``, 1-5) output either high (+12 V) or low (0 V).

:meth:`.pwm_chan()` sets a :abbr:`PWM (pulse width modulation)` output on the
specified channel(``chan``, 1-5). This can be used to effectively set the
channel's output voltage between 0 V (``pwm=0``) and +12 V (``pwm=255``).

:meth:`.hit_hold_chan()` was implemented with solenoid control in mind. Full
+12 V is output on the specified channel (``chan``) briefly, then reduced to the
specified PWM duty cycle (``duty=0.0-1.0``). This reduces power consumption and
heat generated when operating solenoid valves.

.. admonition:: Technical Note

   The PWM carrier wave frequencies differ between output channels:

   +----------+------------------+-------------------------+
   | Channels | Arduino Pins     | PWM Frequency (default) |
   +==========+==================+=========================+
   | 1, 4, 5  | D3, D9, D10, D11 | 31372.55  (490.20) Hz   |
   +----------+------------------+-------------------------+
   |  2, 3    | D5, D6           | 62500.00 (976.56) Hz    |
   +----------+------------------+-------------------------+

   These have been increased from the Arduino defaults, so as to move
   out of the audible range (you/your labmates are welcome).

Arduino Pin Control
===========================
All of the Arduino Nano's microcontroller pins are broken out on the FETbox PCB,
along with 40 unconnected solder pads and power for development. This allows the
end user to connect additional inputs/outputs to customize the FETbox their
application.

The :mod:`.fetbox` module includes basic functionality for serial control of
these additional pins. 

See the official Arduino website for more details about digital and analog pins:
   * https://arduino.cc/en/Tutorial/DigitalPins

   * https://arduino.cc/en/Tutorial/AnalogInputPins

Setting Output Pins
-------------------

:Digital Write: :meth:`digital_write(pin, val) <.digital_write()>`
:PWM: :meth:`analog_write(pin, pwm) <.analog_write()>`

Digital pins (``D0``-``D13``) and analog input pins (``A0``-``A5`` [*]_) can be
both be set to output simple ``LOW`` (0V) or ``HIGH`` (+5V) signals.

>>> # set D7 output to HIGH
>>> my_fetbox.digital_write(7, 1)
>>> # pin D7 now reads +5V

The Arduino Nano is only capable of 'analog' (PWM) output on pins D3, D5, D6,
D9, D10, and D11 - of which, the first five are connected to MOSFET output
channels. All of these can still be controlled with the :meth:`.analog_write()`
method, however, only ``D11`` is completely unused. PWM values are 8-bit
(0-255).

>>> # set D11 to 50% PWM duty cycle
>>> my_fetbox.analog_write(11, 128)
>>> # pin D11 now outputs a +2.5V signal

>>> # set D10 to 20% PWM duty cycle
>>> my_fetbox.analog_write(10, 51)
>>> # pin D10 now outputs +1V, however MOSFET channel #5 also outputs +2.4V


Reading Input Pins
-------------------
:Digital Pins: :meth:`digital_read(pin) <.digital_read()>`
:Analog Pins: :meth:`analog_read(pin) <.analog_read()>`

Arduino input pins can also be queried over the serial interface. Digital pin
names are supplied as an ``int`` (e.g. ``1``), analogs pins names as ``str``
(e.g. "A3").


Digital readings return ``1`` for a ``HIGH`` state, or ``0`` for a ``LOW``
state.

.. code-block:: python

   >>> # Read digital pin 7 (5V signal connected)
   >>> reading = my_fetbox.digital_read(7)
   >>> print(reading)
   >>> 1

Analog readings return a 10-bit value (0-1023) which corresponds to a signal
voltage approximately 0-5V.

.. code-block:: python

   >>> # Read analog pin 3 (3.3V signal connected)
   >>> reading = my_fetbox.analog_read('A3')
   >>> print(reading)
   >>> 700

Digitally reading an analog pin will return the nearest state (``HIGH`` or
``LOW``) corresponding to the input signal.

.. code-block:: python

   >>> # Digital read analog pin 3 (3.3V signal connected)
   >>> reading  my_fetbox.digital_read('A3')
   >>> print(reading)
   >>> 1

.. [*] Analog input pins ``A0``-``A5`` can be read/written digitally, however,
   ``A6`` and ``A7`` are strictly analog-readable only.

Misc. Methods
===================

* :meth:`.heartbeat()` - Pings the *FETbox*, returns ``TRUE`` if responsive.

* :meth:`.query_ID()` - Retrieves the *FETbox's* programmed ID.

4. Expanding Functionality
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Custom Serial Commands
=======================

The :class:`plateflo.fetbox` module has two methods for direct serial
communication, :meth:`.send_cmd()` and :meth:`.send_query()`. These can both
send a arbitrary command to the FETbox Arduino, however expect different
responses; :meth:`.send_cmd()` expects a simple pass/fail response, while
:meth:`.send_query()` expects an arbitrary LF-terminated string response
terminated.

The FETbox firmware can be easily modified to expand the recognized commands
and execute more complex code internally (e.g. reading SPI- or I2C-connected
sensors) before sending an informed response string.

.. note::
   If a custom command requires more than 200ms to execute, increase the serial
   timeout from the default in the pyserial backend:

   >>> my_fetbox.mod_ser.ser.timeout = 1.0 # 1 second timeout

The following serial commands are already defined in ``fetbox.CMD``:

.. pprint:: plateflo.fetbox.CMDS

Firmware Modification
----------------------
FETbox serial commands have the following structure:
   .. code-block::
      
      @<CMD><BODY>\n
      |  |    |    |
      |  |    |    Line feed (LF)
      |  |    |     
      |  |    Command body, arbitrary contents
      |  Command code, single ASCII character
      Command start

``@`` is the start of command character, present at the beginning of every
FETbox serial command.

The ``CMD`` character directs ``cmd_interpret(char* cmd)`` to execute
user-defined code through conditional statements therein.

The ``BODY`` of the command is parsed by user code and is command-specific.

.. code-block:: cpp

   // Existing commands are defined as macros at the top of the .ino program:
   #define CMD_ID        '#'   // query device ID
   #define CMD_YOURCMD   '1'   // your custom command

   void cmd_interpret(char* cmd) {
      /* Module ID query */
      if(cmd[0] == CMD_ID) {
         Serial.print("fetbox");
         Serial.print(ID);
         Serial.write("\n");
      }
   
      /* some other commands */
      else if(cmd[0] == CMD_SOMEOTHERCMD) {
         // does other stuff
         }

      /* your amazing command */
      else if(cmd[0] == CMD_YOURCMD) {
         // do something, no <BODY> parameters
         do_something();

         // or parse <BODY> for parameters, then execute a function
         int _chan = (cmd[1]-'0');  // parse channel #
         disco_time(_chan);         // execute disco on provided channel
         ack();                     // command success response
      }



      // ... etc.


