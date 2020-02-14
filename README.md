# Sensor Fusion - Radar
Radar Target Generation and Tracking

## Project Overiew

This project is for target generation and tracking using Frequency-modulated continuous-wave radar

* calculate the beat signal to find the Range and Doppler Effect. 
* Apply FFT on the Beat Signal to express the signal in Frequency Dimension
* Apply CA-CFAR to avoid False Alarms by varying the Detection-Threshold of Range Doppler Map according to the noise-level.  

### Implementation steps for the 2D CFAR process.

The main idea of this step is to slice a window along the Range-Doppler Map such that
a CUT is thresholded whilst taking margin for lagging cells and leading cells. 

* Create a matrix of same size as RDM to store the filtered/thresholded values
```
[x,y] = size(RDM);
RDM_Filtered = zeros(x,y);
```

* Make a loop for Range-Dimesion which starts after particular marging
```
for R = Tr + Gr + 1 : Nr/2 - (Tr+Gr)
```

* Make another loop for Doppler-Dimension where a margin is considered at start
```
for D = Td + Gd + 1 : Nd - (Td+Gd)
```

* Define Window-Size of which we iterate over the Map
```
range_start = R - Tr - Gr; range_end = R + Tr + Gr;
doppler_start = D - Td - Gd; doppler_end = D + Td + Gd;
```

* Iterate the Window and Calculate the noise of included cells, take average and specify threshold
```
noise_level = db2pow(RDM(range_start : range_end,doppler_start :doppler_end));
average_noise = sum(noise_level) / ntrain;
db = pow2db(average_noise);
threshold = db + offset;
```

* Check for threshold value for CUT, if pass = 1;
```
if RDM(R, D) > threshold
    RDM_Filtered(R, D) = 1;
end
```

### Selection of Training Cells and Guards Cells
Cells have been selected on both dimensions; Range and Doppler

* Range Dimension
```
Tr = 10;
Td = 5;
```

* Doppler Dimension
```
Gr = 5;
Gd = 1;
```

* Set offset Value
```
offset = 20;
```

* Calculate the Total Training Cells for later use in the noise-average step
```
ntrain = (2*Tr+2*Gr+1)*(2*Td+2*Gd+1) - (2*Gr+1)*(2*Gd+1);
```

### Steps Taken to suppress noise on edges

This was achieved by making a zero-matrix of same size as the RDM, theb only add value'1' to cells which pass the threshold value and satisfy the margin-indexing

This way, no noise was included throughout the process. 


## Visualization of Results and Outputs

* FFT
![FFT](https://github.com/Mamdouh93Murad/SFND-Radar/blob/master/Graph/FFT.jpg)

* FFT2 
![FFT2](https://github.com/Mamdouh93Murad/SFND-Radar/blob/master/Graph/FFT2.jpg)

* CFAR
![CFAR](https://github.com/Mamdouh93Murad/SFND-Radar/blob/master/Graph/CA-CFAR.jpg)
