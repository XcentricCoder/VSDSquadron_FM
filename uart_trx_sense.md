### High Level Architecture
This module is a *Finite State Machine* of a UART transmitter module that takes in parallel data and serializes and transmits it bit-by-bit.
According to 8N1 UART protocol , it dictates that the data to be transmitted must have 
*  *1 start bit*
*  *8 data bits*
*  *1 stop bit*
*  *No parity bit*

**Key Components and Their Roles**

1. *Finite State Machine*
   This is the control unit. It stores the current state and reacts to the **senddata** input signal.

2. *Input Buffer Register* **(buf_tx)**:
   An 8-bit shift register.
   * When transmission is initiated (senddata is high and current stae is IDLE state), it loads the parallel data from **txbyte**.
   * In the **TXING** state, it shifts right by one bit on every clock cycle, presenting the next lsb **(buf_tx[0])** as the output.

3. *Bit Counter* **(bits_sent)**:
   An 8-bit counter that resets at the start of every tramsmission.
   It increments for each clock cycle spent in the **TXING** state.
   When it reaches 8, it signals the FSM that the data transmission has been completed triggering the transition to next stage.

4. *Output Registers* **(txbit, txdone)**:
   * *txbit*: Holds the current value of the output bit to be transmitted to tx line.
   This ensures that the output is stable and synchronized to the system clock.
   * *txdone*: A flag register set by FSM to signal that the transmission has been completed and the module is ready for a new byte.

### State transition flow diagram
   The FSM operates in the following sequence :
   1. **IDLE** : The module waits here. Here, **tx** is kept high (Stop bit condition).
      When **senddata** is asserted, it loads **txbyte** into **buf_tx**, clears txdone and moves **STARTTX**.
      
   2. **STARTTX** : This state lasts for one clock cycle and forces start bit low into  **tx** and then transitions to **TXING**.
      
   3. **TXING** : The main data transmission cycle and it remains in the same state for the next 8 clock cycles.
      It left shifts **buf_tx** and places lsb on **tx** and the **bits_sent** counter keeps a track and after the data transmission is done, the state moves on to **TXDONE**.
      
   4. **TXDONE** : This state lasts for one clock cycle and forces stop bit high into  **tx** and sets **TXDONE** signal high. It then transitions to **IDLE** state waiting for new data to transmit.

### Counter logic in the top module
```
  
/* 9600 Hz clock generation (from 12 MHz) */
    reg clk_9600 = 0;
    reg [31:0] cntr_9600 = 32'b0;
    parameter period_9600 = 625;
```
```
 always @(posedge int_osc) begin
    frequency_counter_i <= frequency_counter_i + 1'b1;
        /* generate 9600 Hz clock */
        cntr_9600 <= cntr_9600 + 1;
        if (cntr_9600 == period_9600) begin
            clk_9600 <= ~clk_9600;
            cntr_9600 <= 32'b0;
        end
  end
```

* The 28 bit counter increments on every oscillator clock cycle.
* It generates a 9600 Hz clock from the 12MHz oscillator using counter.
* The parameter period_9600 = 625 is defined as (12MHz/2)/625 = 9600 Hz.
* Hence the counter toggles every 625 cycles.

### UART Transmission module

```uart_tx_8n1 DanUART (.clk (clk_9600), .txbyte("D"), .senddata(frequency_counter_i[24]), .tx(uarttx)); ```

* It instantiates UART protocol in 8n1 format (8 data bits, no parity and 1 stop bit)
* It transmits data 'D' repeatedly.
* senddata is controlled by bit 24 of the counter, causing periodic transmission.
* 9600 Hz clock is used for proper baud rate timing.

 ```
 SB_RGBA_DRV RGB_DRIVER (
    .RGBLEDEN(1'b1                                            ),
    .RGB0PWM (uartrx),
    .RGB1PWM (uartrx),
    .RGB2PWM (uartrx),
    .CURREN  (1'b1                                            ),
    .RGB0    (led_green                                       ), //Actual Hardware connection
    .RGB1    (led_blue                                        ),
    .RGB2    (led_red                                         )
  );
  defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";

endmodule
```
* In the RGB LED driver , the RGB pins are connected to UARTRX signal.
* As a result , all 3 LEDs light up as a result of UART receive activity.
* Hence, it shows that the UART communication is successful.
