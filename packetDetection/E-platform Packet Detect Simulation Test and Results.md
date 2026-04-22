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

### 2.1.3.  11b

## 2.2.  Simulation Results
### 2.2.1.  11a

### 2.2.2.  11b
