# 1. The redesigned and legacy algorithm description based on E-platform  
This section compares the 11a/b packet detection redesigned algorithm prior to the CSFlag1 state with the legacy algorithm, analyzes their core differences, and compares their decision strategies in packet detection.  
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


# 2. 11a/b Simulation Platform Modification and Simulation Results  
This section describes the modifications to the simulation Platform and the corresponding simulation results under the normal operation of the 11a/b packet detection module.  
## 2.1.  Simulation Platform Modification
### 2.1.1.  common  
**(1) add pf signal source**  

Add AWGN signal source on the Rx side of the E-platform for pf simulation.  

**(2) add statistics**  

Add statistics of relevant results in the E-platform, collect package detection results, and conduct performance analysis:
| Results Statistics | Location |
|-------------|-----------|
|CSFlag1 <br> CSFlag2 <br> CSFlag3 <br> DsssDet | in AGCLoop.m |
|L-SIG Decoder |in OFDMHeaderDemod.m|
|mdmOn.ofdm <br> mdmOn.dsss |in ExeCase.m |

**(3) Configuration modification**  

In the normal process of E-platform entering the package detection process, the configuration parameters need to be modified as follows:
| Parameter | Default Value | Modification Value | Location |
|-------------|-----------|----------------|----------------|
|SIM.PerfCrit|snr|pant|in tc_xxx.txt|
|CFG.RXALG|FLPT|FXPT|in defSTAs.txt|
|CFG.AGC|PFCT|FXPT|in defSTAs.txt|
|RF.RFName|NON|KARST|in defSTAs.txt|

### 2.1.2.  11a  
In order to continue the packet detection process even at low SNR (less than 1dB), the configuration needs to be modified as follows：
| Parameter | Default Value | Modification Value | Location |
|-------------|-----------|----------------|----------------|
|RIU.rampUpGap_qdB|16|6|in defSTAs.txt|


### 2.1.3.  11b

## 2.2.  Simulation Results
### 2.2.1.  11a
### 2.2.1.1  Pf  
**Legacy Algo**  

The independent simulation results of AC/XC are as follows:  
 <img src="./figSet/legacy_single_AC_pf.png" width="400" /> <img src="./figSet/legacy_single_XC_pf.png" width="400" />  
 
The Joint simulation results of AC/XC are as follows:
  <img src="./figSet/legacy_1T1R_Joint_pf.png" width="800" />  
   <img src="./figSet/legacy_1T2R_Joint_pf.png" width="800" />  
**Redesign Algo**

### 2.2.1.1  Pm(AWGN)  
**Legacy Algo**  
The overall simulation results are shown below：
  <img src="./figSet/legacy_pf1%25_pm.png" width="800" />  
  <img src="./figSet/legacy_pf01%25_pm.png" width="800" />    
The line in the figure is explained as follows：  
| color | mean || linear | mean || marker | mean |
| - | - |-| - | - |-| - | - |
| red | XC || solid line | no CFO || no marker | 1T1R |
| blue | AC || dashed line | CFO=40ppm || circle | 1T2R |
| green | Joint || - | - || - | - |  

*Conclusion*  
(1) Under the same pf conditions, XC pm performs better than AC;  
(2) with CFO(40ppm), XC performance degradation of about 1dB, AC performance improvement of about 1dB(delay sequence use sign bit);  
(3) Joint Pm:The performance of 1T2R is improved by about 3dB compared to 1T1R; with CFO(40ppm), The overall performance degradation is about 0.5dB;  

**Redesign Algo**

**compare**





### 2.2.2.  11b
