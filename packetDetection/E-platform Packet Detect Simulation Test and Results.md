# 1. The redesigned and legacy algorithm description based on E-platform  
This section compares the 11a/b packet detection redesigned algorithm prior to the CSFlag1 state with the legacy algorithm, analyzes their core differences, and compares their decision strategies in packet detection.  
## 1.1. 11a Algorithm / Strategy Description

**(a) Algorithm Description**
| Algo | Legacy | redesigned |
|-------------|-----------|----------------|
| AC-algo.1 | delay16-auto(delay sequence use sign bit) | delay16-auto(delay sequence use phase info.) | 
| AC-algo.2 | Antennas Comb.(equal weight) | Antennas Comb.(antenna power weight) | 
| AC-algo.3 | ~ | freq. offset(phase) estiamtion from the input sequence | 
| AC-algo.4 | ~ | delay8-auto(delay sequence use phase info.) | 
|-| | |
| XC-algo.1 | Local sequence: non-constant modulus | Local sequence: constant modulus | 
| XC-algo.2 | Antennas Comb.(equal weight) | Antennas Comb.(antenna power weight) | 
| XC-algo.3 | ~ | freq. offset(accumulation phase) compensation to input sequence for cross-correlation | 
|-| | |
| power-algo.1 | Antennas Comb.(equal weight) | Antennas Comb.(antenna power weight) | 
| power-algo.2 | ~ | Antenna weight calculation ( LUT ) | 

**(b) Strategy Description**
| Dec | Legacy | redesigned |
|-------------|-----------|----------------|
| AC-dec| for n = n_0 AC_D16(n) > pho_AC16*P(n)  | for n = n_0 and n_0+16 AC_D16(n) > pho_AC16 *P(n) and AC_D8(n) < pho_AC8 *P(n) | 
|-| | |
| XC-dec | for n = n_0 XC(n) > pho_XC*P(n) | for n = n_0 and n_0+16 XC(n) > pho_XC*P(n)| 


## 1.2. 11b Algorithm / Strategy Description

**(a) Algorithm Description**
| Algo | Legacy | redesigned |
|-------------|-----------|----------------|
| XC-algo.1 | 1us length Cross-Corr(modulus square) | 2us length approximate Maximum Likelihood Cross-Corr(modulus) | 
| XC-algo.2 | ~(only 1 RX antenna used） | Antennas Comb.(antenna power weight) | 
| XC-algo.3 | EWMA (Exponentially Weighted Moving Average) | ~ | 
| XC-algo.4 | Sum of the Top 3 Maximum Values in Each Interval of Length 22 | ~ | 
|-| | |
| power-algo.1 | Antenna power calculation(modulus square)  | Antenna power calculation(modulus) | 
| power-algo.2 | ~ | Antenna weight calculation ( LUT ) | 
| power-algo.3 | ~ | Antennas Comb.(antenna power weight) | 
| power-algo.4 | smoothing | ~ | 

**(b) Strategy Description**
| Dec | Legacy | redesigned |
|-------------|-----------|----------------|
| XC-dec | for n = n_0 XC(n) > pho_XC*P(n) | for n = n_0 and n_0+22 and n_0+44 XC(n) > pho_XC*P(n)| 


# 2. 11a/b Simulation Platform Modification and Simulation Results  
This section describes the modifications to the simulation Platform and the corresponding simulation results under the normal operation of the 11a/b packet detection module.  
## 2.1.  Simulation Platform Modification
### 2.1.1.  11a

### 2.1.2.  11b

## 2.2.  Simulation Results
### 2.2.1.  11a

### 2.2.2.  11b
