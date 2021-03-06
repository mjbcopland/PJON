
**Medium:** Light |
**Pins used:** 1 / 2

AnalogSampling strategy or data link is designed to sample digital data using analog readings and can be used to communicate data wirelessly through light. It is able to use a single LED for both photo-emission and photo-reception phases enabling an half-duplex connection between two or more devices. AnalogSampling can operate wirelessly with a range of maximum 5 meters in ideal conditions using only a pair of LEDs (visible light, infrared or ultraviolet) or can be used along with single channel optic fiber cables enabling point-to-point bidirectional connection between two devices, that can also be branched using optical bidirectional splitters (PLC), enabling an extremely cheap electromagnetic interference free data link.

AnalogSampling can also be used with two pairs of any sort of tuned photo-emitters and photo-receivers enabling for example long range laser wireless communication. It was originally implemented it in the far 2011, here you can see the first [video documented experiment](https://www.youtube.com/watch?v=-Ul2j6ixbmE). It has been recently debugged and updated to act as a PJON Strategy. Take a look at the [video introduction](https://www.youtube.com/watch?v=yIncPe8OPpg) for a brief showcase of its features.

#### Compatibility
- ATmega88/168/328 16MHz (Diecimila, Duemilanove, Uno, Nano, Mini, Lillypad)
- ATmega1280/2560 16MHz (Arduino Mega)

#### Performance
PJON works with the following communication modes:
- `AS_STANDARD` runs at 1024Bd or 128B/s
- `AS_FAST` runs at 1361Bd or 170B/s
- `AS_OVERDRIVE_32` runs at 3773Bb or 471B/s
- `AS_OVERDRIVE_16` runs at 5547Bb or 639B/s
- `AS_OVERDRIVE_8`  runs at 12658Bd or 1528B/s

Caution, `AS_OVERDRIVE_8` sets ADC clock prescale to a higher rate than manufacturer recommends as maximum ADC sample rate (prescale 16).

#### How to use AnalogSampling
Pass the `AnalogSampling` type as PJON template parameter to instantiate a PJON object ready to communicate through this Strategy. All the other necessary information is present in the general [Documentation](/documentation).
```cpp  

  // Predefine AS_MODE selecting the communication mode if needed  
  #define AS_MODE 1 // AS_STANDARD     - 1024Bd  or 128B/s
  #define AS_MODE 2 // AS_FAST         - 1361Bd  or 170B/s
  #define AS_MODE 3 // AS_OVERDRIVE_32 - 3773Bb  or 471B/s
  #define AS_MODE 4 // AS_OVERDRIVE_16 - 5547Bb  or 639B/s
  #define AS_MODE 5 // AS_OVERDRIVE_8  - 12658Bd or 1528B/s

  /* Acknowledge latency maximum duration (15000 microseconds default).
     Could be necessary to higher AS_RESPONSE_TIMEOUT if sending long packets because
     of the CRC computation time needed by receiver before transmitting its acknowledge  */
  #define AS_RESPONSE_TIMEOUT 15000

  /* Set the back-off exponential degree (default 5) */
  #define AS_BACK_OFF_DEGREE      5

  /* Set the maximum sending attempts (default 10) */
  #define AS_MAX_ATTEMPTS        10

  /* The values set above are the default producing a 3.2 seconds
     back-off timeout with 20 attempts. Higher SWBB_MAX_ATTEMPTS to higher
     the back-off timeout, higher SWBB_BACK_OFF_DEGREE to higher the interval
     between every attempt. */

  #include <PJON.h>

  PJON<AnalogSampling> bus;

  void setup() {
    bus.strategy.set_pin(A0);        // Set the pin A0 as the communication pin
                                     // or
    bus.strategy.set_pins(A0, 12);   // Set pin A0 as input pin and pin 12 as output pin  

    bus.strategy.set_threshold(AS_THRESHOLD); // Set threshold (default value AS_THRESHOLD)
  }

```
After the PJON object is defined with its strategy it is possible to set the communication pin accessing to the strategy present in the PJON instance.

#### What can be done?
The most basic example is to connect two devices using a couple of visible light LEDs connected to the A0 pin used as wireless transceivers.

![PJON AnalogSampling LED wireless communication](http://www.pjon.org/assets/images/PJON-AnalogSampling-half-duplex-led-communication.png)

Leveraging of the interesting characteristics of LEDs:

- Emit photons if electrons are travelling through the junction
- Emit electrons if photons are hitting the junction (photo-electric effect)

it is possible to use LEDs as wireless (bidirectional) transceivers!

In most cases it is necessary to add a pull-down resistor with a value around 200KΩ-5MΩ connecting the A0 pin with ground to reduce the LED capacitance, reduce bit transition slopes and electromagnetic induced interference. Higher resistance can higher the range but can also higher background noise.

Consider that depending on the operating voltage of your device LEDs could be overpowered, so choose the LED depending on its operating voltage and add a current limiting resistor if needed. Because of the non continuous power supply also if using a 5v system most LEDs (infrared, blue, white) should survive.  

The obtained range is related to:
- **Resolution** - use `analogReference` wiring method to configure `analogRead` resolution as necessary taking care that the input voltage is not higher than the configured voltage reference
- **Sensitivity** find a couple of identical LEDs, position one shining light directly on the other's junction and test the voltage produced by the unpowered one. Blue LEDs can produce more than 2 volts if hit by a twin LED light at full power
- **Transmission power** the more power you feed to your transmitter, the more range and reliability you can get
- **Signal-to-noise ratio or SNR** Noise level affects communication reliability specially when the signal magnitude is lowered by distance, filtering your input from background noise can higher maximum range and overall communication reliability. If your system has to operate with a strong background noise, consider using a hardware filter to cut the background noise frequencies and use a pull down resistor as described above.

With the necessary hardware choices and timing configuration `AnalogSampling` can be used to experiment with short range infrared or visible light communication (i.e. micro-robot swarm, DIY remote, optic fiber), medium range using lighting as transceivers (i.e. cars transmitting data through front and backlights) or long range laser communication (i.e. data between ground and LEO).  

#### Known issues
- Direct sunlight or other light sources can affect receiver's sensitivity and maximum communication range
- A pull-down resistor can be necessary to obtain nominal functionality, see above
