### High Level Architecture
This module is a *Finite State Machine* that takes in parallel data and serializes and  then transmits it bit-by-bit.
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





![flow_diagram](https://github.com/XcentricCoder/VSDSquadron_FM/blob/main/flow_diagram.png)


Here is a sample working demonstration!!! ( some issues with baud rate mismatching tho :(  )

https://drive.google.com/file/d/1vX1oIuS5FNC11nf37iFO1x-FHPZcLoZC/view?usp=drive_link















