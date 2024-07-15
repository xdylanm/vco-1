# vco-1
Sawtooth core VCO based on Moritz Klein's DIY VCO. 

The general design and test description can be found on Moritz Klein's channel. The oscillator core is a ramp generator driven by a Schmitt trigger inverter and capacitor, controlled by an NPN. 

## Notes

* Using 2N3904 (NPN) and 2N3906 (PNP)
* Implemented a different triangle wave generator based on a precision rectifier circuit. The triangle generator sums a buffered ramp and an inverted, rectified ramp with balancing incorporated with a trimmer on the rectifier sum input.
* Made the coarse adjust an internal trimmer: "factory" tuning involves adjusting the coarse bias on the PNP base with both the coarse level adjust (high side) and divider scaling (low side). Might as well get that set on the bench then fine tune in operation.
* Option to configure the pulse output as a non-inverting summer (from Synth Mages) rather than open loop comparator.
* There are other variants without the Schmitt trigger inverter (e.g. hyestersis in another op-amp). There is also a 12V tolerant single Schmitt trigger inverter (TC4S584F) as an option for a future SMT version. 

## References

1. Moritz Klein: [DIY VCO Series](https://youtube.com/playlist?list=PLHeL0JWdJLvTuGCyC3qvx0RM39YvopVQN&si=mLFLNtdBjMHdq1OI)
2. Precision rectifier circuit: [Circuit Digest](https://circuitdigest.com/electronic-circuits/half-wave-and-full-wave-precision-rectifier-circuit-using-op-amp)
3. Synth Mages: [DIY VCO variant](https://gitea.circuitlocution.com/synth_mages/MK_VCO)
