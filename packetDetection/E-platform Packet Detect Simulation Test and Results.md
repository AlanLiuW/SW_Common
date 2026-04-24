# 1. The redesign and legacy algorithm solution description  
Compare the different PD algorithm solutions on E-platform prior of the CSFlag1, including algorithms & decision strategies.
## 1.1. 11a Algorithm / Strategy Description

**(a) Algorithm Description**
| Algo | Legacy | redesigned |
|-------------|-----------|----------------|
| AC-algo.1 | delay16-auto(delay sequence use sign bit,modulus) | delay16-auto(delay sequence use phase info,modulus) | 
| AC-algo.2 | Antennas Comb.(equal weight) | Antennas Comb.(antenna power weight) | 
| AC-algo.3 | ~ | freq. offset(phase) estiamtion from the input sequence | 
| AC-algo.4 | ~ | delay8-auto(delay sequence use phase info,modulus) | 
|-| | |
| XC-algo.1 | 1us length Cross-Corr(modulus) | 1us length Cross-Corr(modulus)) | 
| XC-algo.2 | Local sequence: non-constant modulus | Local sequence: constant modulus | 
| XC-algo.3 | Antennas Comb.(equal weight) | Antennas Comb.(antenna power weight) | 
| XC-algo.4 | ~ | freq. offset(accumulation phase) compensation to input sequence for cross-correlation | 
|-| | |
| power-algo.1 | Moving average antenna power calculation(modulus)  | Moving average antenna power calculation(modulus) | 
| power-algo.2 | Antennas Comb.(equal weight) | Antennas Comb.(antenna power weight) | 
| power-algo.3 | ~ | Antenna weight calculation ( LUT ) | 

**(b) Strategy Description**
| Dec | Legacy | redesigned |
|-------------|-----------|----------------|
| AC-dec=true| for n = n0 <br> AC_D16(n) > $\rho_{AC16}$*P(n)  | for n = n0 and n0+16 <br> AC_D16(n) > $\rho_{AC16}$ *P(n) and AC_D8(n) < $\rho_{AC8}$ *P(n) | 
|-| | |
| XC-dec=true | for n = n0 <br> XC(n) > $\rho_{XC}$*P(n) | for n = n0 and n0+16 <br> XC(n) > $\rho_{XC}$*P(n)| 
|-| | |
| Joint AC/XC-dec=true | AC-dec=true or XC-dec=true | AC-dec=true or XC-dec=true | 


## 1.2. 11b Algorithm / Strategy Description

**(a) Algorithm Description**
| Algo | Legacy | redesigned |
|-------------|-----------|----------------|
| XC-algo.1 | 1us length Cross-Corr(modulus square) | 2us length approximate Maximum Likelihood Cross-Corr(modulus) | 
| XC-algo.2 | ~(only 1 RX antenna used） | Antennas Comb.(antenna power weight) | 
| XC-algo.3 | Exponentially Weighted Moving Average(1 feedback IIR, delay22) | ~ | 
| XC-algo.4 | Sum of the Top 3 Maximum Values in Each Interval of Length 22 | ~ | 
|-| | |
| power-algo.1 | Instance antenna power calculation(modulus square,L=1)  | Moving average antenna power calculation(modulus,L=44) | 
| power-algo.2 | ~ | Antenna weight calculation ( LUT ) | 
| power-algo.3 | ~ | Antennas Comb.(antenna power weight) | 
| power-algo.4 | Smoothing(1 order TimeVariable IIR) | ~ | 

**(b) Strategy Description**
| Dec | Legacy | redesigned |
|-------------|-----------|----------------|
| XC-dec = true | for n = n0 <br> XC(n) > $\rho_{XC}$*P(n) | for n = n0 and n0+22 and n0+44 <br>  XC(n) > $\rho_{XC}$*P(n)| 


# 2. 11a/b Simulation Platform Description
## 2.1.  Simulation Environment Change Description
(1) Enable the AWGN source on the Rx-side for the $P_{f}$-performance verification   
(2) Add statistical variables to collect intermediate simulation results and obtain performance indicators  
(3) Modify the configuration parameter to ensure the normal operation of the package detection process  

| Results Statistics | Location | | Parameter | Default Value | Modification Value | Location |
|-------------|-----------|-|-------------|-----------|----------------|----------------|
| CSFlag1-3 | in AGCLoop.m | |SIM.PerfCrit|snr|pant|in tc_xxx.txt|
| DsssDet | in AGCLoop.m | |CFG.RXALG|FLPT|FXPT|in defSTAs.txt|
|L-SIG Decoder |in OFDMHeaderDemod.m| |CFG.AGC|PFCT|FXPT|in defSTAs.txt|
|mdmOn.ofdm、mdmOn.dsss |in ExeCase.m | |RF.RFName|NON|KARST|in defSTAs.txt|

### 2.2.  11a PD Modify Description
In order to continue the packet detection process even at low SNR (less than 1dB), the configuration needs to be modified as follows：
| Parameter | Default Value | Modification Value | Location |
|-------------|-----------|----------------|----------------|
|RIU.rampUpGap_qdB|16|6|in defSTAs.txt|

### 2.3.  11b PD Modify Description

## 3. Simulation Results
Based on the adjustment of the simulation platform, the simulation results are as follows.  
## 3.1. 802.11a Simulation Results
### 3.1.1. Threshold simulation
**Legacy Algo**  

The independent simulation results of AC/XC are as follows:  
 <img src="./figSet/legacy_single_AC_pf.png" width="400" /> <img src="./figSet/legacy_single_XC_pf.png" width="400" />  
 
The Joint simulation results of AC/XC are as follows:  
  <img src="./figSet/legacy_1T1R_Joint_pf.png" width="400" /> <img src="./figSet/legacy_1T2R_Joint_pf.png" width="400" />  
**Redesign Algo**

### 3.1.2.  $P_{m}$(AWGN) simulation
**Legacy Algo**  
The overall simulation results are shown below, linear and color introduction:  
solid：no CFO，dash：CFO=40ppm；red：XC，blue：AC，green：Joint;  
  <img src="./figSet/legacy_pf1%25_pm.png" width="500" /> <img src="./figSet/legacy_pf01%25_pm.png" width="500" />    

*Conclusion*  
(1) Under the same $P_{f}$ conditions, XC $P_{m}$ performs better than AC;  
(2) with CFO(40ppm), XC performance degradation of about 1dB, AC performance improvement of about 1dB(delay sequence use sign bit);  
(3) Joint simulation: 1T2R vs 1T1R, have Gain about 3dB; with CFO(40ppm), the performance degradation is about 0.5dB;  

**Redesign Algo**

**The redesign and legacy algorithm performance comparison** 


### 3.2.  802.11b Simulation Results
