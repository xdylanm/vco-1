# vco-1
Sawtooth core VCO based on Moritz Klein's DIY VCO. 

The general design and test description can be found on Moritz Klein's channel. The oscillator core is a ramp generator driven by a Schmitt trigger inverter and capacitor, controlled by an NPN. 

## Notes

* Using 2N3904 (NPN) and 2N3906 (PNP)
* Implemented a different triangle wave generator based on a precision rectifier circuit. The triangle generator sums a buffered ramp and an inverted, rectified ramp with balancing incorporated with a trimmer on the rectifier sum input.
* Made the coarse adjust an internal trimmer: "factory" tuning involves adjusting the coarse bias on the PNP base with both the coarse level adjust (high side) and divider scaling (low side). Might as well get that set on the bench then fine tune in operation.
* Option to configure the pulse output as a non-inverting summer (from Synth Mages) rather than open loop comparator.
* There are other variants without the Schmitt trigger inverter (e.g. hyestersis in another op-amp). There is also a 12V tolerant single Schmitt trigger inverter (TC4S584F) as an option for a future SMT version. 

## Oscillator Core Theory of Operation

The core of the oscillator is composed of the capacitor C1 and NPN BJT Q1. C1 is charged by the Schmitt trigger inverter U1A through D1 and discharged by Q1. Adjusting the bias on the base of Q1 adjusts the discharge rate of C1. 

The hysteresis on the CD40106 inverter is approximately 3V for a 12V supply (measured 2.8V on the bench). Assuming tuning over six octaves from 16Hz (~$C_0$ ) to 1024Hz (~$C_6$), the discharge time should range from approximately 1ms to 100ms. Using a 2.2nF capacitor for C1, the range of collector currents in Q1 is
$$
I_C \sim C\frac{dV}{dt} \to \left\{\begin{array}{ll}66nA & t_{discharge}=100ms \\ 6.6\mu A & t_{discharge}=1ms\end{array}\right.
$$
The basic model for the collector current is
$$
I_C = I_S \left(e^{\frac{V_{BE}}{V_T}} - 1\right)\to V_{BE}=V_T \ln\left(\frac{I_C}{I_S}+1\right)\simeq V_T(\ln I_C-\ln I_S)
$$
Assuming $I_S=10^{-13}A$, $V_{BE}$ ranges from 350mV to 470mV  ($V_T=26mV$). For larger $I_S$ , the low end of the range for $V_{BE}$ will be reduced (e.g. 290mV for $I_S=10^{-12}A$ ). The span of the range remains constant.

PNP BJT Q2 acts as a buffer in an emitter-follower configuration and counteracts the thermal response of Q1. Since the base voltage of Q1 should be below saturation (approximately 0.4V), there is an 11V drop across R1, which results in a bias current of approximately $11\mu A$ into the emitter and $V_{EB}\sim 0.5V$ for Q2. 

The  CV input is designed for a range between 0 and 5V (V/oct). $V_{BE}$ should vary by 90-120mV, which corresponds to a scaling of 0.018 to 0.024. The resistor divider formed by R6+TH1 and R5+RV1 creates this scaling: R6+TH1 is 110k such that R5+RV1 should range between 2k and 2.6k (R5=1.5k and RV1 ranges from 0 to 2k).

**Tuning Note:** Use an input that varies by 1V (e.g. alternate between 1V and 2V). If the difference in frequency between the same note in adjacent octaves is less than a factor of 2, increase RV1 to increase the gain of the resistor divider.

To set the low end of the range, the network including R19, R20, and RV3 is used to set a bias voltage. This is summed with the CV input through R7+TH3. As with the CV input, R7+TH3 creates a resistor divider with R5+RV1, which determines the gain applied to the bias voltage. Since $V_{EB}$ of Q2 is $\sim 0.5V$ and the low end of the range for $V_B$ of Q1 is 250-350mV, the base of Q2 is biased between -250mV and -150mV (more range here is better depending on the transistor characteristics - the tradeoff is tuning sensitivity). A factor of 40 reduces -12V to -300mV. R19 will limit the low end of the range and R20 will limit the high end. 

**Ideal Tuning**

* Assume $f_{C_0}=16Hz$ is the low frequency reference such that $t_{discharge,0} = 62.5ms$. 

* Use $\Delta V$ from the inverter hysteresis ($\Delta V = 3V$) and C1=2.2nF to obtain 
  $$
  I_{C,0} = C \frac{dV}{dt} = C \Delta V f_{C_0} = 2.2\cdot 10^{-9} \cdot 3 \cdot 16 = 106\mathrm{nA}
  $$

* Determine $V_{BE,0}$ and $\Delta V_{BE}$ , where $V_{BE}$ is the bias voltage to establish current $I_{C,0}$ through the NPN and $\Delta V_{BE}$ is the change in $V_{BE}$ to double the current,  
  $$
  \begin{align*}
  \Delta V_{BE} &\equiv nV_T \ln 2I_{C} - nV_T\ln I_C = nV_T \ln 2 \\
  V_{BE,0} &\equiv nV_T\left(\ln I_{C,0} - \ln I_{S}\right)
  \end{align*}
  $$

* Adjust the gain of the voltage divider (RV1) such that a change of 1V results in a doubling of the current $I_C$ (set $\Delta V_{BE} \approx 18\mathrm{mV}$).

* Adjust the offset with the coarse tuning pot such that the base of the NPN is biased to $V_{BE,0}$ when the input CV is 0V. 



 



## References

1. Moritz Klein: [DIY VCO Series](https://youtube.com/playlist?list=PLHeL0JWdJLvTuGCyC3qvx0RM39YvopVQN&si=mLFLNtdBjMHdq1OI)
2. Precision rectifier circuit: [Circuit Digest](https://circuitdigest.com/electronic-circuits/half-wave-and-full-wave-precision-rectifier-circuit-using-op-amp)
3. Synth Mages: [DIY VCO variant](https://gitea.circuitlocution.com/synth_mages/MK_VCO)
