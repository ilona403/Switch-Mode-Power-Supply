# 1. Input Circuit

1. Soldered **BR1, F1-F2, J1, R15-R16, R99** from the *Input* and test points for `PRI_GND` & `UNREG_DC`. Place 12A fuse.
   	- AC output on `UNREG_DC` with rocker switch OFF: No Output
   	- AC output on `UNREG_DC` with rocker switch ON:
	![InputCircuit_BR1](Images/InputCircuit_BR1.png)
2. Desoldered **BR1** and solder on **D1-D4** from the *Input*.
	- AC output on `UNREG_DC` with rocker switch OFF: No Output
	- AC output on `UNREG_DC` with rocker switch ON:
	![InputCircuit_Diode](Images/InputCircuit_Diode.png)
3. **BR1** vs **D1-D4** results: **D1-D4** had a lower voltage drop compared to **BR1**. Will continue with **D1-D4** for future testing.

4. Tested AC input waveform.
	![InputCircuit_AC](Images/InputCircuit_AC.png)

5. Soldered on **C9-C11** from the *Input*.
6. Tested the filtered input:
	- AC output on `UNREG_DC`: 33.8V DC output, less than 0.5% ripple.
	![InputCircuit_Cap_Ripple](Images/InputCircuit_Cap_Ripple.png)
	- Capacitor discharged to 0V after 60 seconds after rocker switch is OFF.

# 2. Independent Channel A 
## Channel A LVDD
1. This is to test the `A_5V` & `A_3V3` rail.
2. Soldered on **C1, C3, C4, C5, C12, D41, L1, L5, R94, U2** from the *LVDD* and test points for `A_GND`, `A_5V`.
3. Checked the `A_5V` rail.
	- DC output on `A_5V` had ringing noise at a frequency of 42 kHz.
  	![5V_Ringing](Images/5V_Ringing.png)
  	- 5V red LED turned on.
	- Ignoring the ringing, the `A_5V` net has a max of 4.99V and a min 4.91V
  	![5V_Clean](Images/5V_Clean.png)
	- `PRI_GND` & `A_GND` are isolated (using a voltmeter, ohmmeter, and continuity check).
4. Soldered on **C2, C6, D40, U1, R93** & test point for `A_3V3` from the *LVDD*.
5. Checked the `A_5V` rail again.
   	- DC output on `A_5V` had an average output of 4.87V. The ringing was reduced with the **C2** capacitor.
   	  ![5V_With_3V3](Images/5V_With_3V3.png)
7. Checked the `A_3V3` rail.
	- DC output on `A_3V3` had an average output of 3.17V.
	- 3V3 yellow LED turned on.
	![3V3](Images/3V3.png)

## Channel A ESP32
1. Tested the ESP32 powers on. 
2. Soldered on **J7, J8**.
3. Placed the ESP32 on the board.
	- LED on the ESP32 turned on.
4. Soldered on **Q5, Q7, R17, R18** and test points `BUCK_A_nPWM`, `BUCK_A_PWM`, & `OUT_A_LV`.
5. Determined the accuracy of PWM duty cycle & logical level shift voltage levels.
	- Ran the Channel_A_ESP32_PWM.cpp file.
		- The PWM was set to 10kHz with a 10% duty cycle. `BUCK_A_PWM` showed exactly 10% and `BUCK_A_nPWM` had 90%. The PWM from the ESP32 has an accurate duty cycle.
		![Channel_A_Buck_PWM_Accuracy_10kHz](Images/Channel_A_Buck_PWM_Accuracy_10kHz.png)
			- Logical level shifts between `A_GND` & `A_5V` for `BUCK_A_PWM` and`BUCK_A_nPWM` at 10kHz.
   		 	![Channel_A_Buck_PWM_10kHz](Images/Channel_A_Buck_PWM_10kHz.png)
     			![Channel_A_Buck_nPWM_10kHz](Images/Channel_A_Buck_nPWM_10kHz.png)
     
	- Test was then run for 50kHz.
		- The PWM was set to 50kHz with a 10% duty cycle. `BUCK_A_PWM` showed approximately 10% and `BUCK_A_nPWM` had 90%. The PWM is accurate also at 50kHz.
		![Channel_A_Buck_PWM_Accuracy_50kHz](Images/Channel_A_Buck_PWM_Accuracy_50kHz.png)
			- Logical level shifts between `A_GND` & `A_5V` for `BUCK_A_PWM` and`BUCK_A_nPWM` at 50kHz.
   		 	![Channel_A_Buck_PWM_50kHz](Images/Channel_A_Buck_PWM_50kHz.png)
     			![Channel_A_Buck_nPWM_50kHz](Images/Channel_A_Buck_nPWM_50kHz.png)
     

## Channel A External Connections Part 1
1. Tested the input button & LCD screen for Channel A.
2. Soldered on external JST connectors, **R5 & R7** for Channel A labelled **LCD_A, BUTTON_A**.
3. Tesedt to make sure these connections work.
	- Ran the Channel_A_Ext_Conn_1.cpp file.
		- LCD correctly displayed BUTTON LOW or BUTTON HIGH depending on the button state.
   		- The button itself also lit up the internal LED.

## Channel A Temperature Sensor
1. Tested the temperature sensor.
2. Soldered on **C37, R46, R47, U7**.
3. Soldered on $51k\Omega$ for **R54**.
	- Ran the Channel_A_Temp_Sense.cpp file.
		- Used a hot air gun set at $100^{\circ}C$. Temperature sensor sent LOW to *A_TMP_SNSE* when the hot air gun was pointed at it. Temperature sensor sent HIGH in room temperature.
		- The temperature sensor should go off at around $52^{\circ}C$.
4. Finalized on a resistor value for **R54** is to determine a temperature cut off.
	- $50k\Omega$ for $53^\circ C$ threshold.


## Channel A Transformers
1. This is to test the flyback & feedback transformer. 
2. Soldered on the following circuits so that the feedback transformer can work (read the "SMPS Testing Results.md" to see why this is necessary). 
    - Soldered on a **$8.2k\Omega$** on the `UNREG_DC` **$6.8k\Omega$** on `PRI_GND`, creating a voltage division of 15.1V and a 10uF electrolytic capacitor at the voltage division.
    - Soldered on the **UCC5304DWV** to the board. Use enamel wire to connect:
		- Pin 1: *A_DRIVE*
   		- Pin 2 & 3: *A_5V*
   		- Pin 4: *A_GND*
   		- Pin 5 & 6: *PRI_GND*
   		- Pin 7: the gate of Q29
   		- Pin 8: The 15.1V line made from the voltage division.
3. Tested the new feedback transformer replacement circuit.
    - Ran the Channel_A_Transformer_Gate.cpp file.
		- 10-11V Output (Probably loading effect). PWM signal at 50kHz with a 25% duty cycle at 10V.
4. Soldered on **C24, C25, C26, Q29, Q38, Q39, R70, R101, T2** and test points `FLY_OUT_A`, `PRI_FET_A` & `SEC_FET_A`.
	- Ran the Channel_A_Transformer1.cpp file.
		- Smoke came out immediately, the `PRI_GND` copper exploded and continuity between the ground plane was gone. The 5V buck was also damaged. Also removed **Q29, Q38**.
   		- Added a new buck and a `PRI_GND` wire. Circuit worked fine without running the PWM output.
		- Added new **Q29, Q38** transistors and ran the script again. Smoke once again came from the thermistor and transformer. 12A fuse was blown. Circuit is pulling too much current.
	- Also noticed that the previous PCB also had damage to the `PRI_GND` trace.
		
