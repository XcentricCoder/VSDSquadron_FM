# Comprehensive report of the task: LED blinking

## Understanding of Verilog Code
  * The first section of the verilog code descries the module instantiations of various input and output wires.
    * __led_red, led_blue and led_green__: These are the output wires instatiated to control the colour of RGB lights with logic of 1 or 0.
    * __hw_clk__: The external clk of the board that provides clock signals to the module's timings.
    * __testwire__: It is the output wire that is assigned to the 6th bit of the frequency counter.
  
      
  * __Internal component analysis__
    * __Internal Oscillator (SB_HFOSC)__
      * __CLKHF_DIV__ : It controls clock division of the internal oscillator.
             * 0b00 - 48MHz
             * 0b01 - 24MHz
             * 0b10 - 12MHz
             * 0b11 - 6MHz
           Here it is instantiated to 0b10 , implies the internal clock is set to frequency of 12MHz.

      * __CLKHFPU__ (Clock High Frequency Power Up) : Must be set to 1 to power up the oscillator.
      * __CLKHFEN__ (Clock High Frequency Enable) : Must be set to 1 to drive the output.
      * __CLKHF__ (Clock High Frequency) : The output oscillator clock that goes as an input signal to the internal clock.
    
    * __Frequency Counter__
        *  __frequency_counter_i__ : A 28-bit counter that increments with every positive clock edge.
        *  __testwire__ : output wire that has been assigned the 6th bit of the frequency counter, ie. it divides frequency by 2^5 = 32. Therefoe the frequency of the clk would be 3.75 kHz. 

    * __RGB LED driver__ <BR>
      This is the dedicated hard block on FPGA the drives the LED blinking on the FPGA. It provides the current control and the LED control for each LED.
      * __RGBLEDEN__ : Enables for the whole driver, must be 1.
      * __CURREN__ : Enables the current control.
      * __RGB0, RGB1, RGB2__ : Provides PWM signals for the LEDs, 1 implies LED is on and 0 means off.
      * __RGB_DRIVER.RGBX_CURRENT__ : Parameter controls the current levels in the circuit thereby adjusting the brightness level of the LEDs. The values range from 1 to 63.
        
  ## Purpose of the verilog code <br>
  The verilog code provides the blue light to glow in the controlled  manner. It provides a stable internal clock as well as RGB LED control with timing and current control capabilities.

  ## Functionalities of the RGB driver
  The RGB driver drives the hard FPGA block. <br>
  * The PWM pins provides for the LED turning on for Blue LED(1'b1) and turn off for Green and Red LEDs.
  * Current control outputs for each of the LEDS through the PWM pins.
  * 1'b0 means the LEDs are turned off whereas 1'b1 means the LEDs are turned on with
      * RGB_DRIVER.RGBX_CURRENT having current value of 0b0000001 for minumum brghtness 
      * RGB_DRIVER.RGBX_CURRENT having current value of 0b1111111 for maximum brghtness
  * The physical pin mapping of the FPGA board
     * __RGB0__ -> Red LED (pin 39)
     * __RGB1__ -> Green LED (pin 40)
     * __RGB2__ -> Blue LED (pin 41)
  

  
