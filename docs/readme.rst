2200087 Serial Protocol and Standalone Usage
############################################

The 2200087 is an inexpensive DMM sold at radioshack. It supports logging and 
graphing data on a computer, but the supplied code only supports Windows. This 
is a python script to allow for connecting to the multimeter over USB on Linux 
or Mac OSX. Due to radioshack not supplying any serial specifications, the below 
protocol was reverse engineered from simply observing the output of the DMM.
 
This package and script, and much of the readme file herein, is essentially a 
slightly tweaked version of the script written and maintained by David Dworken, 
available at https://github.com/ddworken/2200087-Serial-Protocol.git  

Installation and Usage
======================

Start by cloning this repository::

    git clone https://github.com/chintal/2200087-Serial-Protocol.git

or the original by David Dworken::

    git clone https://github.com/ddworken/2200087-Serial-Protocol.git

Then install dependencies::

    pip install numpy pyserial

Then you're ready to go. Just run the program to display a text output of the data::

    sudo python serialDecoder.py -p /dev/ttyUSB0
 
If you want a graph as your output, first install GNUPlot::

    sudo apt-get install gnuplot

then run::

    sudo python serialDecoder.py -p /dev/ttyUSB0 --graph

You also can read from multiple multimeters at the same time and get a 
CSV output like so::

    sudo python serialDecoder.py -p /dev/ttyUSB0 /dev/ttyUSB1

If you only want the actual values and not information about what mode 
the multimeter is on, use the -q flag::

    sudo python serialDecoder.py -p /dev/ttyUSB0 -q

Protocol Description
====================

+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
|         | Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3      | Bit 2    | Bit 1     | Bit 0    | 
+=========+=======+=======+=======+=======+============+==========+===========+==========+ 
| Byte 1  | 0     | 0     | 0     | 1     | Minus      | AC       | SEND      | AUTO     |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 2  | 0     | 0     | 1     | 0     | Continuity | Diode    | Low Batt  | Hold     | 
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 3  | 0     | 0     | 1     | 1     | MAX        | E4       | F4        | A4       |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 4  | 0     | 1     | 0     | 0     | D4         | C4       | G4        | B4       |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 5  | 0     | 1     | 0     | 1     | DP3        | E3       | F3        | A3       |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 6  | 0     | 1     | 1     | 0     | D3         | C3       | G3        | B3       |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 7  | 0     | 1     | 1     | 1     | DP2        | E2       | F2        | A2       |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 8  | 1     | 0     | 0     | 0     | D2         | C2       | G2        | B2       |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 9  | 1     | 0     | 0     | 1     | DP1        | E1       | F1        | A1       |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 10 | 1     | 0     | 1     | 0     | D1         | C1       | G1        | B1       |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 11 | 1     | 0     | 1     | 1     | Percent    | HFE      | Rel Delta | MIN      |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 12 | 1     | 1     | 0     | 0     | u (1e-6)   | n (1e-9) | dBm       | Seconds  |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 13 | 1     | 1     | 1     | 1     | Farads     | Amps     | Volts     | m (1e-3) | 
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+
| Byte 14 | 1     | 1     | 1     | 0     | Hz         | Ohms     | K (1e3)   | M (1e6)  |
+---------+-------+-------+-------+-------+------------+----------+-----------+----------+

All bytes are sent over in hexadecimal numbered one through fourteen. Bytes 3-4 
contain digit 4, bytes 5-6 contain digit 3 and so on. All other parts of the 
display are turned on as shown in the above table. The multimeter sends the data 
at a rate of 10 Hz. 

.. image:: https://cloud.githubusercontent.com/assets/5304541/6250379/6ab9de40-b75b-11e4-9444-c7d69e58e5ff.png
.. image:: https://cloud.githubusercontent.com/assets/5304541/6250469/03216f4a-b75c-11e4-92eb-9b6d7568b3a8.png
