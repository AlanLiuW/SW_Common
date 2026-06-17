
# Implementation 

## 1. 11a detection
### 1.1. modules and interface
#### 1.1.1. External modules

| Module name | Instances | Input bitwdith | Output bitwidth | Description |
|-------------|-----------|----------------|-----------------|-------------|
| cordic_foe | 1 | 12-bit I and Q | Angle, TBD (10 for now) | Frequency offset angle estimation | 
| cordic_foc | 1 per antenna, 2 total | 12-bit I and Q and 10-bit angle | 13-bit I and Q | FOE derotation CORDIC. Scaled output with CORDIC factor |

#### 1.1.2. Internal modules

| Module name | Instances | Input bitwdith | Output bitwidth | Description |
|-------------|-----------|----------------|-----------------|-------------|
| cordic_mag | 2, 1 per antenna | 12-bit I and Q | 12-bit mag and 10-bit angle | Compute magnitude and angle for auto-correlation rotation |
| - |  |  |  |  |
| fixed_scaling_cordic | 2, 1 per antenna | 12 bits | 12 bits | CORDIC output scaling down - divide by 1.6468 |
| delay_line_8_first | 2, 1 per antenna | 10 bits | 10 bits | Delay line for auto-corr angle first 8 taps. |
| delay_line_8_second | 2, 1 per antenna | 10 bits | 10 bits | Delay line for auto-corr angle second 8 taps |
| - |  |  |  |  |
| cordic_rot_auto16 | 2, 1 per antenna | 12-bit A and 10 bit angle | 13-bit I and Q | Perform auto-correlation preprocess for 16-tap delay line：(A,angle->I,Q) |
| running_sum_auto16 | 4, I and Q and 2 antennas | 13 bits | 19 bits | Running sum for auto-correlation. Max depth 64, covering 16, 32, 48. |
| antenna_weighted_combine | 2, I and Q | 19 bits ant1 and ant2 | 19 bits | Antenna weighted combine of auto-correlation sum 16. One for I, one for Q. |
| mod_approx_auto16 | 1 | 19-bit I and Q | 19-bit mag out | Approximation computation for modulus |
| thresh_auto16 | 1 | 19-bit weighted auto sum and weighted mag sum | 1 bit | Threshold module for auto-correlation, 16 tap, above threshold. |
| - |  |  |  |  |
| cordic_rot_auto8 | 2, 1 per antenna | 12-bit A and 10 bit angle | 13-bit I and Q | Perform auto-correlation preprocess for 8-tap delay line：(A,angle->I,Q) |
| running_sum_auto8 | 4, I and Q and 2 antennas | 13 bits | 19 bits | Running sum for auto-correlation. Max depth 64, covering 16, 32, 48. |
| mod_approx_auto8 | 2, 1 per antenna | 19-bit I and Q | 19-bit mag out | Approximation computation for modulus |
| antenna_weighted_combine | 1 | 19 bits ant1 and ant2 | 19 bits | Antenna weighted combine of auto-correlation sum 8. |
| thresh_auto8 | 1 | 19-bit weighted auto sum and weighted mag sum | 1 bit | Threshold module for auto-correlation, 8 tap, below threshold. |
| - |  |  |  |  |
| fixed_param_crosscorr | 2, 1 per antenna | 13-bit I and Q | 19-bit I and Q | Fixed parameter cross correlation filter using adders and subtractors. | 
| mod_approx_cross | 2, 1 per antenna | 19-bit I and Q | 19-bit mag out | Approximation computation for modulus. |
| antenna_weighted_combine | 1 | 19 bits ant1 and ant2 | 19 bits | Antenna weighted combine of cross-correlation sum. |
| thresh_cross | 1 | 19-bit weighted cross sum and weighted mag sum | 1 bit | Threshold module for cross correlation, above threshold. |
| - |  |  |  |  |
| running_sum_mag | 2, 1 per antenna |  12 bits | 18 bits | Running sum for power/magnitude. Max depth 64, covering 16, 32, 48. |
| weight_comp_lut | 1 | 18-bit ant1 and ant2 | 3-bit ant1 and ant2 | Compute weight for each antenna from mag sum |
| antenna_weighted_combine | 1 | 18 bits ant1 and ant2 | 18 bits | Antenna weighted combine of mag sum. |
| - |  |  |  |  |
| dynamic_scaling_19_to_12 | 2, I and Q | 19 bits | 12 bits | Scaling from 19 bits to 12 bits to be used by cordic_foe | 

#### 1.1.3. Inputs and outputs

| Signal name | Input/Output | Bitwidth | Description |
|-------------|--------------|----------|-------------|
| x | Input | 12 bits | Signal I input |
| y | Input | 12 bits | Signal Q input |
| foe | Output | 10 bits | Estimated FOE vector |
| thresh_exceed_auto16 | Output | 1 bit | Autocorrelation output decision result |
| thresh_below_auto8 | Output | 1 bit | Autocorrelation output decision result |
| thresh_exceed_cross | Output | 1 bit | Cross correlation output decision result |

### 1.2. reference design
**top-level design diagram**
 <img src="./fig_Preamble_Detection_design/11a_PD_design_diagram_toplevel_1.png" />

**lower-level design diagram**  
 <img src="./fig_Preamble_Detection_design/11a_PD%20design%20diagram_lowerlevel_1_CUMSUM.png" width="500" />
 <img src="./fig_Preamble_Detection_design/11a_PD%20design%20diagram_lowerlevel_2_approMod.png" width="400" />  
<img src="./fig_Preamble_Detection_design/11a_PD%20design%20diagram_lowerlevel_3_ACWeight.png" width="500" />
 <img src="./fig_Preamble_Detection_design/11a_PD%20design%20diagram_lowerlevel_4_AC.png" width="350" />  
  <img src="./fig_Preamble_Detection_design/11a_PD%20design%20diagram_lowerlevel_5_MovingSUM_c1.png" />  

**Module Abbreviation Reference Table**  
| Module name | design diagram | | Module name | design diagram |
|-------------|--------------|-|--------------|-------------|
| cordic_foe | FOE(Cordic-V) | | cordic_foc | FOC(Cordic-III)| 
| cordic_mag | Cordic-I | | fixed_scaling_cordic | scaling_cordic | 
| delay_line_8_first | (located in) Pre-Process | | delay_line_8_second | (located in) Pre-Process |
| cordic_rot_auto16 | Cordic-IV | | running_sum_auto16 | CUMSUM in Auto-Correlation | 
| antenna_weighted_combine | Antenna Combine | | mod_approx_auto16 | appro. modulus |
| thresh_auto16 | Decision| |cordic_rot_auto8 | Cordic-II |
| running_sum_auto8 | CUMSUM in Interf.Meas | | mod_approx_auto8 | appro. Modulus in Interf.Meas | 
| antenna_weighted_combine | Antenna Combine | | thresh_auto8 | Decision | 
| fixed_param_crosscorr | MovingSUM || mod_approx_cross | appro. Modulus in Cross-Correlation | 
| antenna_weighted_combine | Antenna Combine | |thresh_cross | Decision |
| running_sum_mag | CUMSUM in Auto-Correlation | |weight_comp_lut* | A.C.Weight |
| antenna_weighted_combine | Antenna Combine | |dynamic_scaling_19_to_12 | Dynamic scaling | 

The LUT in the weight_comp_lut module is shown below (used for both 11a/b)  
 <img src="./fig_Preamble_Detection_design/LUT.png" width="500" />

## 2. 11b detection
### 2.1. modules and interface
#### 2.1.1. External modules

#### 2.1.2. Internal modules

| Module name | Instances | Input bitwdith | Output bitwidth | Description |
|-------------|-----------|----------------|-----------------|-------------|
| delay_add_1tap | 4, I and Q and 2 antennas | 6 bits | 7 bits | input add the delayed-1 input |
| fixed_param_crosscorr | 2, 1 per antenna | 7-bit I and Q | 11-bit I and Q | Fixed parameter cross correlation filter using adders and subtractors |
| delay_add_22tap | 4, I and Q and 2 antennas | 11-bit I and Q | 12-bit I and Q | combine the same consecutive 2 preamble bits and sum them up |
| delay_sub_22tap | 4, I and Q and 2 antennas | 11-bit I and Q | 12-bit I and Q | combine the different consecutive 2 preamble bits and sum them up |
| delay_line_22 | 4, I and Q and 2 antennas | 11 bits | 11 bits | delay line for sum up the cross-correlation in "delay_add_22tap" & "delay_sub_22tap" modules |
| mod_approx_crosscorr | 4, 2 possible preamble-bits combinations and 2 antennas | 12-bit I and Q | 12-bit mag out | Approximation computation modulus |
| max_select | 2, 1 per antenna | 12-bit | 12-bit | Select the data with the maximum modulus value |
| antenna_weighted_combine | 1 | 12 bits ant1 and ant2 | 12 bits | Antenna weighted combine of cross-correlation sum |
| thresh_cross | 1 | 12-bit weighted cross sum and weighted mag sum | 1 bit | Threshold module for cross correlation, above threshold |
| - |  |  |  |  |
| mod_approx_pwr | 2, 1 per antenna | 6-bit I and Q | 6-bit mag out | Approximation computation modulus for every truncted-input |
| runing_sum_mag | 2, 1 per antenna | 6-bit mag | 12-bit mag out | Running sum for magnitude |
| weight_comp_lut | 1 | 12-bit ant1 and ant2 | 3-bit ant1 and ant2 | Compute weight for each antenna from mag sum |
| antenna_weighted_combine | 1 | 12 bits ant1 and ant2 | 12 bits | Antenna weighted combine of mag sum |

#### 2.1.3. Inputs and outputs

| Signal name | Input/Output | Bitwidth | Description |
|-------------|--------------|----------|-------------|
| x | Input | 12 bits | Signal I input |
| y | Input | 12 bits | Signal Q input |
| thresh_exceed_cross | Output | 1 bit | Cross correlation output decision result |

### 2.2. reference design
**top-level design diagram** 
 <img src="./fig_Preamble_Detection_design/11b_PD_design_diagram_toplevel_1.png" />
 
**lower-level design diagram**  
 <img src="./fig_Preamble_Detection_design/11b_PD%20design%20diagram_lowerlevel_1_barkercodecorr.png" width="400" />
  <img src="./fig_Preamble_Detection_design/11b_PD%20design%20diagram_lowerlevel_2_CUMSUM.png" width="200" />
   <img src="./fig_Preamble_Detection_design/11b_PD%20design%20diagram_lowerlevel_3_approMod.png" width="300" />

**Notes**  
* 1、A.C.Weight and Antenna Combine refer to section 11a above.

**Module Abbreviation Reference Table**  
| Module name | design diagram | | Module name | design diagram |
|-------------|--------------|-|--------------|-------------|
| delay_add_1tap | Pre-process | |fixed_param_crosscorr | Barker code Correlation |
| delay_add_22tap | (located in) CUMSUM || delay_sub_22tap | (located in) CUMSUM |
| delay_line_22 | (located in) CUMSUM || mod_approx_crosscorr | appro. modulus | 
| max_select | MAX || antenna_weighted_combine | Antenna Combine | 
| thresh_cross | Comp. || mod_approx_pwr | appro. modulus |
| runing_sum_mag | see toplevel || weight_comp_lut* | A.C.Weight | 
| antenna_weighted_combine | Antenna Combine | ||
