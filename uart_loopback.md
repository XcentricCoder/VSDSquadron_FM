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
   * In the **txing** state, it shifts right by one bit on every clock cycle, presenting the next lsb **(buf_tx[0])** as the output.

3. *Bit Counter* **(bits_sent)**:
   
