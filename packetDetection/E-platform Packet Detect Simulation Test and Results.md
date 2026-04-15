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


## 1.2. 11b Algorithm / Strategy Comparison
### 1.2.1. Algorithm Comparison


### 1.2.2. Strategy Comparison
**redesigned Detection Strategy (fs=22M)**  
（1）Cross-Correlation Detection Strategy
- Sliding window detection (detection completed within 6μs)  
  Take every 22 points as one detection window, and judge by window: whether there exist points in the window that meet the following condition:  
  <div align="center">XC > P * XC_Thr </div>  
  If satisfied, mark the window as detection passed and record the positions of the points meeting the condition in the window.  
- Continuous judgment  
  Among all windows marked as detection passed, if there exist **3 consecutive passed windows** with a point interval of 22 and 44 between windows, the cross-correlation (XC) detection is judged as passed.

**legacy Detection Strategy**  
（1） Cross-Correlation Detection Strategy  
 - If any point within the full sampling range meets the following condition, the cross-correlation (XC) detection is judged as passed:  
   <div align="center">XC > P * XC_Thr </div> 


# 2. 11a/b Simulation Platform Modification and Simulation Results  
This section describes the modifications to the simulation Platform and the corresponding simulation results under the normal operation of the 11a/b packet detection module.  
## 2.1.  Simulation Platform Modification
### 2.1.1.  11a

### 2.1.2.  11b

## 2.2.  Simulation Results
### 2.2.1.  11a

### 2.2.2.  11b
