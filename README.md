#Inside the World of IR Hacking: Turn Your TV Remote into a Science Experiment

When speaking of hardware hacking, we can’t overlook one of the oldest, and still valuable and usable, transmitting method as Infra-Red transaction. It’s still in use in our homes, offices, and sometimes in manufacturing. It has its own disadvantages, such as a limited transmission range, sensitivity to light, slow speed, and the possibility of being intercepted and reused (including replay attacks). However, on the other hand, it’s inexpensive to produce, easy to use, and offers long battery life for remotes, as it consumes electricity only during operation. Today we are going to talk about IR reverse engineering aka hardware hacking against IR remote protocols. 

0. Preparation
--------------

Before diving into the subject, we need to prepare the necessary hardware and software. The main application we are going to use is digital oscilloscope. I know at least 2 of them: Logic 2 from Saleae, Inc (https://www.saleae.com/pages/downloads) and PulseView from Sigrok (https://sigrok.org/wiki/Downloads). Both of them are fully free and you can easily download and install it onto your PC or Mac.

But, speaking of PulseView, it can also be installed onto Linux OS and it’s fully opensource, comparing to the proprietary Logic 2. Also, PusleView has least 3 IR analyzers from the box (NEC, RC-5, RC-6), but Logic 2 does not. Definitely, you can install additional analyzers from the internet, but it’s an additional headache as you have to find the proper one (hopefully not bundled with a trojan), properly install all the libraries and cross your fingers it’s working. Speaking of me, I have both of them installed on my PC, but I prefer working with Logic 2 as it’s more convenient and does not have critical mistakes when you have to close/open the app after each measurement.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-001.png)
Logic 2 / PulseView overview	

Now let’s talk about the hardware we need, which involves more than just an application. And we would love to start from the breadboard. A breadboard is a reusable platform for prototyping and testing electronic circuits without soldering. It consists of rows and columns of small, conductive sockets into which electronic components can be inserted. It is super convenient to place jump wires and our next guest – IR receiver.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-002.png)
Breadboard	

An IR receiver is an electronic device that detects and processes infrared light signals. It is commonly used in devices like TVs, remote-controlled gadgets, and automation systems to receive commands from an IR transmitter, such as a remote control. In our particular project we are using the IR receiver from the Elegoo 37 Sensor Kit (https://spot.pcc.edu/~dgoldman/labs/37SENSORKIT.pdf). It has 3 pins, where G is ground, R is Vcc (in our case it’s up to 5V volts) and Y is an output (for transmitting the payload). The Wiring diagram how to connect the IR receiver you can find on the page 107 of the provided tutorial.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-003.png)
IR Receiver	
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-004.png)
Wiring diagram	

Next, without what we can’t connect anything – bunch of jump wires. Basically, what types of wires you will need to use depends on your connection scheme. But we would highly recommend to have all types of them: male-male, male-female, female-female; as you don’t know at the start of the project which of them will be useful to you.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-005.png)
Jump wires	

Another one (and we would say, one of the most important equipment), is Logic Analyzer. It is a debugging tool used to capture, analyze, and visualize digital signals in a circuit. Logic Analyzer helps to diagnose issues, monitor communication protocols (like I2C, SPI, or UART), and verify timing relationships between multiple signals.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-006.png)	
Logic analyzer	

The last one is an external DC power supply, at least capable to output 3.3 volts. You can use whatever you like or have, but in our case, we are using Raspberry Pi 4B. That little guy has at least 8 Ground pins, two 5 Volts pins and one 3.3 Volts pin. The biggest pros is that those pins are predefined by design and you don’t need to program it in any way (good for beginners, right). If you have, for instance, Orange Pi or Arduino, it is suitable as well but you have to find the correct pins to be connected to.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-007.png)
Raspberry Pi GPIO pins	


1. Bringing it all together
---------------------------

Once you have everything you need to start performing IR reverse engineering, it’s time to bring them all together to the working state.

First, let’s take the IR receiver and plug it into the breadboard. Please take care of the placing order: IR pins have to be placed in the different number lines, for instance into 33, 34 and 35 number holes. Please, don’t plug IR into the one line, like into row number 35. The specific is, that all of those 5 holes of each line are connected together hence if you plug the sensor in that position all the pins will be connected together and the scheme won’t work.

Once you have done it, take 2 male-male jump wires and connect them in the next order. Breadboard line number 33 (sensor’s ground) to the blue line above the main circuit, breadboard line number 34 (sensor’s Vcc) to the red line above the main circuit. If you did everything correctly you have to have a scheme similar to ours.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-008.png)
Connecting the IR	

Next move is to provide the electricity to our board. Take your Raspberry Pi (or whatever you have as an external power supply) and 2 male-female jump wires. Plug the female end of one wire into the 1st Raspberry Pi pin (exactly where the 3.3 Volts are) and another end to the red line above the main circuit. Simultaneously, plug the female end of the second wire into the 6th Raspberry Pi pin (exactly where the ground is) and another end to the blue line above the main circuit.

If you did everything correctly you have to have a scheme similar to ours.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-009.png)
Connecting the Raspberry Pi	

Once it’s done, connect the Raspberry Pi to the power supply cable. Some of LEDs on the PC’s board should start blinking showing the device is on and loading. But for us it does not matter as once RPi is connected to the electricity, our IR receiver starts working immediately.

Next, take any or your IR remotes, for instance from TV or AC, and press any button on that like power. The small LED on the IR receiver circuit board should start blinking indicating the transmitting is going well. 
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-010.png)
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-011.png)
IR transmitting	

Great, it’s working well so far.

The last thing we have to connect is the logic analyzer. Before plugging it into the PC, take 2 male-female wires. Connect one wire to the channel 0 (or 1, depends on the labeling on the logic analyzer) or any other based on your preferences; and the other end to the breadboard line number 35 (exactly where the Y pin of your IR receiver).

Then, connect the second wire with the logic analyzer ground; and another end to the blue line above the main circuit. Without this, we cannot transmit the intercepted signal to our PC. Once it’s done you are free to connect the USB both ends to the logic analyzer and the PC respectively.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-012.png)
Logic analyzer connection	


2. Intercepting and analyzing
-----------------------------

Alrighty 🙂 IR receiver is connected and working (at least it’s reacting to the remote); and logic analyzer is plugged into the PC. Basically, the kit is set up and now it’s time to do some magic!

Now let’s run a digital oscilloscope. With Logic 2 from Saleae there is no issues at all: once you launch it, it will detect our hardware automatically and set up everything by itself.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-013.png)
Logic 2	

But when we are talking about PulseView – it’s not that straightforward. Once you run the application, you need to choose the device by clicking in the <No Device> button.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-014.png)
PulseView	

On the Step 1: Choose the driver – select fx2lafw (generic driver for FX2 based LAs).
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-015.png)
PulseView: Choose the driver	

On the Step 2 keep everything as it is by default – USB interface.

Step 3 is just clicking the button – Scan for devices using driver above. If your logic analyzer is connected, on the Step 4 the application will automatically identify the hardware.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-016.png)
PulseView: Select the device	

Other stuff which we are going to do is almost the same on both apps hence no need to duplicate it. As we mentioned above, we would use Logic 2 as our main app for this particular exercise.

Before starting recording signals, we have to set up the bit rate and size of recording. For IR transmitting the bit rate of 1 megabit per second (MS/s) is more than enough as well as 1 GB of memory size. Actually, if we are talking about some common IR protocols like NEC or RC5, the carrier frequency is 38 and 36 kHz and that speed would be enough. But we would suggest to keep 1 MS/s just for extra sure.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-017.png)
Logic 2: Settings	

Now, the truth moment – press the Run button. You may see the line on channel 0 started floating. Once it’s, take your remote and press any button, but in our case it’s Power button. If you did everything correctly, you could see the actual signal.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-018.png)
Logic 2: Harvesting the signal	

Great, we intercepted the IR signal, which is coming from the remote to the TV. The next step is to understand how it’s working and basically decode it.

First, there is a header (sometimes engineers also call it as leader code and pause). This is a pulse and space that immediately precedes the data. Usually the pulse is quite long (several ms), and is used by the receiver to adjust its gain control for the strength of the signal.

If we are talking about NEC standard, the leader code is 9 ms, followed by a pause of 4.5 ms. But in our case (Samsung TV), the leader code is 4.5 ms as well as the pause – 4.4 ms. Please take into consideration, those time periods may have some faults and during our measurement there will be slightly different numbers.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-019.png)
Logic 2: Header	

Like on this particular picture, you may see we’ve taken 2 similar signals, but the time is different each time.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-020.png)	
Logic 2: Time difference	

After the header we have the data word – actual signal/payload which controls the TV. So, short pulse (in the bottom) followed by long pause (on the top) is basically 1 bit and equals – 1 or TRUE (red on the picture). On the other hand, short pulse followed by the short pause – 0 or FALSE (green on the picture).
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-021.png)	
Logic 2: Signal defining

Following that pattern lets define the full signal for Samsung TV remote for Power button.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-022.png)	
Logic 2: The signal

After coloring each bit, let’s count them all: 3 reds (mean 111) followed by 5 greens (mean 00000). Then again 3 reds (mean 111) followed by 6 greens (000000). After that 1 red (1) followed by 6 greens (000000). And lastly 1 red (1), 1 green (0) and finally 6 reds (111111).

Now, let’s combine them together:

11100000111000000100000010111111

We believe this view is not convenient hence let’s enhance it.

First, let’s split this date into pieces of 8 bits:

11100000 11100000 01000000 10111111

Wow, this view is much better and we already can see a pattern as first 2 baits (1 bait is 8 bit) are the same.

Great, but usually no one is using the raw binary code as it’s long tough to read. Usually, we are converting the binary into HEX or decimal.

If you are reading this article, we believe you know how to convert numbers; but if you are a beginner, here you are some tips.

First, you may use an online calculator CyberChef: just implement 2 modules – from binary and to hex. The result is here – E0 E0 40 BF.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-023.png)	
https://gchq.github.io/CyberChef/#recipe=From_Binary('Space',8)To_Hex('Space',0)&input=MTExMDAwMDAgMTExMDAwMDAgMDEwMDAwMDAgMTAxMTExMTE	

Second, you may use your offline PC calculator. The result is the same.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-024.png)	
Calculator

Another one converter is through the command line. Linux has an application called bc – arbitrary-precision decimal arithmetic language and calculator. As attributes it takes input (-I) and output (-O) values, and the number to decode (-e).
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-025.png)	
bc	

In this case we asked bc to convert our binary (-I 2) payload (-e) into decimal (-O 10) and hex (-O 16). As you may see the result is the same hence, it’s up to you to choose the most convenient approach..

 

The final result for our Samsung TV remote Power button:

|Button|BIN|DEC|HEX|
|-|-|-|-|
|Power|11100000 11100000 01000000 10111111|3772793023|E0E040BF

The same way you may intercept and decode other buttons.

 
3. Signals’ library
-------------------

During preparation for this article, we tried to find any IR databases which stored main vendors’ signals. Unfortunately, except flipper zero (https://github.com/UberGuidoZ/Flipper-IRDB) and probonopd (https://github.com/probonopd/irdb) github repository, we were not able to get something specific (or maybe we did not OSINTed enough ;).

Moreover, the signals are stored in a very unique way, which can be understood only by particular device. For instance, if we take the same TV remote we observed in this article, and open Flipper’s database (https://github.com/UberGuidoZ/Flipper-IRDB/blob/main/TVs/Samsung/Samsung_BN59.ir) we can find the next:

name: Power
type: parsed
protocol: Samsung32
address: 07 00 00 00
command: E6 00 00 00

Or, taking the same from the second repository – https://github.com/probonopd/irdb/blob/master/codes/Samsung/Unknown_BN59-00685A/7%2C7.csv

functionname             protocol          device              subdevice        function
KEY_POWER                NECx2              7                      7                      2

As you may see, even between each other they don’t have something in common, except the number 7. Also, none of them don’t have the clear raw signal so the engineer can read it and understand properly.

Because of that we decided to create the new GitHub project from scratch, which will store not only the binary code by itself, but also decoded in DEC and HEX formats. Also, we will store the intercepted signal in .SAL format, so you can download and read it in Saleae application. Moreover, we decided to disassemble remotes, so you can see what is inside and how it’s working, including the datasheets of chips.

The full address to our rep is here – https://github.com/IvanGlinkin/Default-IR-vendors-samples

It is divided by vendor’s name and subdivided by the device, like TV or fireplace. The final one directory contains Datasheet, Remote pictures and Samples folders.
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-026.png)	
![](https://raw.githubusercontent.com/IvanGlinkin/media_support/refs/heads/main/HackingIR-027.png)	
https://github.com/IvanGlinkin/Default-IR-vendors-samples	

If you want to participate and contribute to this repository, you are more then welcome. But please, let’s keep it organized and follow the structure.

 
4. Conclusion
-------------
 

Infrared (IR) technology remains a valuable communication method, even with its limitations such as low range, sensitivity to light, and vulnerability to attacks. Its affordability, ease of use, and low energy consumption make it a staple in many devices. This article provided an in-depth guide to reverse engineering IR remote protocols, covering everything from preparing the required hardware and software to intercepting and decoding signals.

We demonstrated how to set up an IR receiver with a logic analyzer, capture signals using software like Logic 2 or PulseView, and decode these signals into binary, decimal, and hexadecimal formats. Additionally, we highlighted the challenges in finding standardized IR signal libraries and introduced a new GitHub repository aimed at addressing this gap by storing raw signal data alongside decoded formats and additional technical details.

By following this guide, you now have the tools to delve into the fascinating world of IR reverse engineering. Whether you’re an enthusiast or a professional, this knowledge can help you uncover the inner workings of IR-based communication and potentially contribute to the growing open-source community.
Post navigation
