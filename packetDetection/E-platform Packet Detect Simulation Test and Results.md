# 1. 11a/b Embedded and Inherent Comparison  
This section compares the 11a/b packet detection Embedded algorithm prior to the CSFlag1 state with the Inherent algorithm, analyzes their core differences, and compares their decision strategies in packet detection.  
## 1.1. 11a Algorithm / Strategy Comparison
### 1.1.1. Algorithm Comparison


### 1.1.2. Strategy Comparison
**Embedded Detection Strategy (fs=20M)**  
（1）Cross-Correlation Detection Strategy
- Sliding window detection (detection completed within 4μs)  
  Take every 16 points as one detection window, and judge by window: whether there exist points in the window that meet the following condition:  
  <div align="center">XC > P * XC_Thr </div>  
  If satisfied, mark the window as detection passed and record the positions of the points meeting the condition in the window.  
- Continuous judgment  
  Among all windows marked as detection passed, if there exist **2 consecutive passed windows** with a point interval of 16 between windows, the cross-correlation (XC) detection is judged as passed.

（2）Auto-Correlation Detection Strategy
- Sliding window detection (detection completed within 4μs)  
  Take every 16 points as one detection window, and judge by window: whether there exist points in the window that meet the following conditions:
  <div align="center">AC16 > P * AC16_Thr and AC8 < P * AC8_Thr </div>  
  If satisfied, mark the window as detection passed and record the positions of the points meeting the condition in the window.

- Continuous judgment  
  Among all windows marked as detection passed, if there exist **2 consecutive passed windows** with a point interval of 16 between windows, the auto-correlation (AC) detection is judged as passed.

**Inherent Detection Strategy**  
（1） Cross-Correlation Detection Strategy  
 - If any point within the full sampling range meets the following condition, the cross-correlation (XC) detection is judged as passed:  
   <div align="center">XC > P * XC_Thr </div> 

（2） Auto-Correlation Detection Strategy  
 - If any point within the full sampling range meets the following condition, the auto-correlation (AC) detection is judged as passed:  
   <div align="center">AC16 > P * AC16_Thr </div>  

## 1.2. 11b Algorithm / Strategy Comparison
### 1.2.1. Algorithm Comparison


### 1.2.2. Strategy Comparison
**Embedded Detection Strategy (fs=22M)**  
（1）Cross-Correlation Detection Strategy
- Sliding window detection (detection completed within 6μs)  
  Take every 22 points as one detection window, and judge by window: whether there exist points in the window that meet the following condition:  
  <div align="center">XC > P * XC_Thr </div>  
  If satisfied, mark the window as detection passed and record the positions of the points meeting the condition in the window.  
- Continuous judgment  
  Among all windows marked as detection passed, if there exist **3 consecutive passed windows** with a point interval of 22 and 44 between windows, the cross-correlation (XC) detection is judged as passed.

**Inherent Detection Strategy**  
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
