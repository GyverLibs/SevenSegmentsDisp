# SevenSegmentsDisp
**Library under active development, support for most display modules is planned.**
Fast and functional library for 7-segment display modules TM74HC595
built on the basis of the shift registers of the same name, without dynamic indication.
To ensure normal operation, it is necessary to manually maintain the dynamic
display indication by calling the .tickManual() method with a period of 500-5000 µs using
timer interrupts or .tick() in a non-blocking loop.
- Maintenance of dynamic indication by the built-in timer on micros ()
- Manual maintenance of dynamic indication if necessary
- Support for numbers with a minus sign
- Output int with and without zeros on the left
- Output float with the specified precision with and without zeros on the left
- Float output with automatic precision selection
- Displaying a character to the desired position, does not touch the rest of the familiarity
- Output an array of characters (0 - 4 pcs.) with left / right alignment
- Output 4 characters on the display manually
- Output of running lines (blocking function)
- Control any of the display points
- Display cleaning
- Display overflow error detection and output (-OVF and OVF)
- Software adjustment of display brightness

### Compatibility
Compatible with all Arduino platforms (using Arduino functions)

## Content
- [Install](#install)
- [Initialization](#init)
- [Usage](#usage)
- [Example](#examcranberry (ple)
- [Versions](#versions)
- [Bugs and feedback](#feedback)

<a id="install"></a>
## Installation
- The library can be found by the name **SevenSegmentsDisp** and installed through the library manager in:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Download library](https://github.com/GyverLibs/SevenSegmentsDisp/archive/refs/heads/main.zip) .zip archive for manual installation:
    - Unzip and put in *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Unzip and put in *C:\Program Files\Arduino\libraries* (Windows x32)
    - Unpack and put in *Documents/Arduino/libraries/*
    - (Arduino IDE) automatic installation from .zip: *Sketch/Include library/Add .ZIP library…* and specify the downloaded archive
- Read more detailed instructions for installing libraries [here] (https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE% D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)

<a id="init"></a>
## Initialization
```cpp
// specify pins
Disp595 (uint8_t dio, uint8_t sclk, uint8_t rclk);
```

<a id="usage"></a>
## Usage
```cpp
void tick(void); // Serving dynamic indication with built-in timer on micros()
void tickManual(void); // Maintenance of dynamic indication manually (to be placed in a timer interrupt, etc.)
void displayIntNulls(int16_t data); // Output int with zeros on the left
void displayInt(int16_t data); // Output int without zeros on the left
void displayFloatNulls(float data, uint8_t base); // Output float with zeros on the left, indicating the number of characters (1 - 3)
void displayFloatNulls(float data); // Output float with zeros on the left, 1 decimal place
void displayFloat(float data, uint8_tbase); // Output float without zeros on the left, indicating the number of characters (1 - 3)
void displayFloat(float data); // Output float without zeros on the left, 1 decimal place
void displayByte(uint8_t a, uint8_t b, uint8_t c, uint8_t d); //Direct output of 4 bytes (characters) to the display
void point(uint8_t pos, bool state); // Add / remove a point at position 0-3 (left)
void clear(void); // Clear display
void brightness(uint8_t bright) // Set brightness value (0 - 7), default maximum (if DISP595_BRIGHTNESS is declared)
void runningString(uint8_t *str, uint8_t size, uint16_t ms) // Output a running string of size size, with a delay of ms, inside the tick function NOT SERVICED
void runningString(uint8_t *str, uint8_t size, uint16_t ms, bool useTick); // Similar, but you can manually enable/disable the built-in tick() function

Defines that can be declared BEFORE linking the library:
DISP595_BRIGHTNESS -> Announce to enable programmatic display brightness adjustment
DISP595_BRIGHTNESS_DEPTH number -> Program brightness depth, default 8 gradations, more - smoother adjustment, stronger display flickering without increasing the frequency of indication.
DISP595_FAST_PERIOD number -> Period in µs of dynamic indication in the built-in timer of the tick method for mode WITH BRIGHTNESS
DISP595_SLOW_PERIOD number -> Same, but for standard mode (constant brightness)

Constants:
ALIGN_LEFT - left alignment of an incomplete string
ALIGN_RIGHT - right-alignment of an incomplete string
```

<a id="example"></a>
## Example
See **examples** for other examples!
```cpp
/*
   Demonstration of all functionality, display of various data on the TM74HC595 display
   Maintenance of dynamic indication takes over the hardware Timer 2
   https://alexgyver.ru/lessons/timer-isr/
*/

#define DATA_PIN 11
#define SCLK_PIN 10
#define RCLK_PIN 12
#define DISP_PERIOD 3000 // Dynamic display period in µs (500-6000)
// #define DISP_PERIOD 500 // If dimming is used, tick() must be pulled more often!

// #define DISP595_BRIGHTNESS // Use brightness control
// #define DISP595_BRIGHTNESS_DEPTH 10 // Brightness depth can be changed(default 8), more - smoother brightness, but more flickering of the display
        
#include <GyverTimers.h>
#include <SevenSegmentsDisp.h>

Disp595 disp(DATA_PIN, SCLK_PIN, RCLK_PIN);

void setup() {
  Timer2.setPeriod(DISP_PERIOD); // Start Timer 2 for interrupts with the desired period
  Timer2.enableISR(); // Enable interrupts Timer 2
}

void loop() {

  /* Output built-in and custom characters, raw data to the display element */
  disp.displayBytes(_H, _E, _L, _P); // Direct output of data (characters) to the display
  disp.point(0, true); // AFTER outputting data, you can add / remove points
  disp.point(3, true); // Specify the position of the point on the left (0 - 3) and turn it on / off (true / false)
  delay(1000);

  /*
     Display brightness software control
     The greater the depth of brightness - the more often you need to pull tick()!
     (if DISP595_BRIGHTNESS is declared before connection)
  */
  /*
    disp.brightnessDepth(8); // Setting the "depth" of brightness - the number of gradations, by default 8 gradations

    for (uint8_t i = 7; i > 0; i--) {
      disp.brightness(i); // Brightness in the range 0-7
      delay(500);
    }

    delay(1000);

    for (uint8_t i = 0; i < 8; i++) {
      disp.brightness(i); // Brightness in the range 0-7
      delay(500);
    }
  */


  /* Output single characters to the desired position LEFT */
  disp.displayByte(_L, 3); // Change 3rd character from 'P' to 'L'
  delay(1000);


  /* Output an array of characters to the display with left / right alignment */
  uint8_t data[] = {_D, _A};
  disp.clear();
  disp.displayBytes(data, sizeof(data)); // Output char array left justified
  delay(1000);

  disp.clear();
  disp.displayBytes(data, sizeof(data), ALIGN_RIGHT); // Output char array right justified
  delay(1000);

  disp.clear();
  disp.displayBytes(data, sizeof(data), ALIGN_LEFT); // Output array of characters aligned to lCranberries to the left
  delay(1000);


  /* Output crawl lines */
  uint8_t str[] = {_1, _2, _3, _4, _5, _empty, _t, _E, _S, _t};
  disp.runningString(str, sizeof(str), 300); // Output of the running line, specify the array of characters, size, delay in ms


  /* Output integers without zeros on the left */
  for (int8_t i = -50; i < 50; i++) {
    displayInt(i);
    delay(100);
  }


  /* Output integers with zeros on the left */
  for (int8_t i = 50; i > -50; i--) {
    disp.displayIntNulls(i);
    delay(100);
  }
  
  /* Print floating point numbers without zeros on the left */
  display.displayFloat(-12.34); // Without specifying precision, fractional signs - 1
  delay(1000);
  display.displayFloat(-12.34, 1); // Or the number of characters can be entered manually, from 1 to 3
  delay(1000);
  display.displayFloat(12.34, 2); // You can output both positive and negative numbers
  delay(1000);
  display.displayFloat(1.234, 3); // But when outputting 3 decimal places, there is not enough space for the minus sign
  delay(1000);


  /* Print floating point numbers with zeros on the left */
  display.displayFloat(-3.14); // Everything is similar here, but with zeros on the left
  delay(1000);
  display.displayFloat(-1.34, 1);
  delay(1000);
  display.displayFloat(3.1415, 2);
  delay(1000);
  display.displayFloat(3.1415, 3);
  delay(1000);


  /* Output number + characters */
  for (uint8_t i = 0; i < 30; i++) {
    int value = random(0, 99);
    disp.displayInt(value); // FIRST output the int number
    disp.displayByte(_t, 0); // Empty characters can be manually filled with characters
    disp.displayByte(_equal, 1);
    delay(200);
  }


  /* Output number + character array */
  uint8_t strData[] = {_P, _H};
  display.displayFloat(1.2, 1); // FIRST print the float number (only took 2 characters)
  disp.displayBytes(strData, sizeof(strData)); // Empty characters can be filled with an array of characters
  delay(1000);

  /* Clear display */
  disp.clear(); // The display buffer is cleared
  delay(3000);
}

ISR(TIMER2_A) {// Interrupt Timer 2
  disp.tickManual(); // Maintenance of dynamic display indication
}
```

<a id="versions"></a>
## Versions
- v1.0

<a id="feedback"></a>
## Bugs and feedback
When you find bugs, create an **Issue**, or better, immediately write to the mail [alex@alexgyver.ru](mailto:alex@alexgyver.ru)
The library is open for revision and your **Pull Request**'s!