# EXP 3 : IIR-BUTTERWORTH-FITER-DESIGN

## AIM: 

 To design an IIR Butterworth filter  using SCILAB. 

## APPARATUS REQUIRED: 
PC installed with SCILAB. 

## PROGRAM (LPF): 
```
clc;
clear;
close;

// ---- Fixed Specifications ----
wp = %pi/4;      // Passband frequency (rad/sample)
ws = %pi/2;      // Stopband frequency (rad/sample)
alphap = 1;      // Passband attenuation (dB)
alphas = 15;     // Stopband attenuation (dB)
T = 1;           // Sampling time

// ---- Prewarping ----
omegap = (2/T)*tan(wp/2);
omegas = (2/T)*tan(ws/2);

// ---- Filter Order ----
N = log10((10^(0.1*alphas)-1)/(10^(0.1*alphap)-1)) / (2*log10(omegas/omegap));
N = ceil(N);

// ---- Cutoff Frequency ----
omegac = omegap / ((10^(0.1*alphap)-1)^(1/(2*N)));

// ---- Analog Filter ----
hs = analpf(N, 'butt', [0,0], omegac);

// ---- Bilinear Transformation ----
z = poly(0, 'z');
Hz = horner(hs, (2/T)*((z-1)/(z+1)));

// ---- Frequency Response ----
HW = frmag(Hz, 512);
w = 0:%pi/511:%pi;

// ---- Plot ----
plot(w/%pi, abs(HW));
xlabel('Normalized Frequency (×π rad/sample)');
ylabel('Magnitude');
title('Butterworth IIR LPF Frequency Response');

// ---- Display Key Results ----
disp("Filter Order N = "), disp(N);
disp("Cutoff Frequency omegac = "), disp(omegac);
disp("Digital Transfer Function H(z) = "), disp(Hz);
```


## PROGRAM (HPF): 
```
clc;
clear;
close;

// ---- Specifications ----
wp = %pi/2;      // Passband (HIGH)
ws = %pi/4;      // Stopband (LOW)
alphap = 1;      // Passband attenuation (dB)
alphas = 15;     // Stopband attenuation (dB)
T = 1;

// ---- Prewarping ----
omegap = (2/T)*tan(wp/2);
omegas = (2/T)*tan(ws/2);

// ---- Filter Order ----
N = log10((10^(0.1*alphas)-1)/(10^(0.1*alphap)-1)) / (2*log10(omegap/omegas));
N = ceil(N);

// ---- Cutoff Frequency ----
omegac = omegap / ((10^(0.1*alphap)-1)^(1/(2*N)));

// ---- Analog LPF Prototype ----
hs = analpf(N, 'butt', [0,0], 1);   // normalized LPF

// ---- LPF → HPF Transformation (IMPORTANT FIX) ----
s = poly(0, 's');
hs_hp = horner(hs, omegac ./ s);   // s → wc/s

// ---- Bilinear Transformation ----
z = poly(0, 'z');
Hz = horner(hs_hp, (2/T)*((z-1)/(z+1)));

// ---- Frequency Response ----
HW = frmag(Hz, 512);
w = 0:%pi/511:%pi;

// ---- Plot ----
plot(w/%pi, abs(HW));
xlabel('Normalized Frequency (×π rad/sample)');
ylabel('Magnitude');
title('Butterworth IIR HPF Frequency Response');

// ---- Display ----
disp("Filter Order N = "), disp(N);
disp("Cutoff Frequency omegac = "), disp(omegac);
disp("H(z) = "), disp(Hz);

```

## OUTPUT (LPF) : 
<img width="763" height="323" alt="image" src="https://github.com/user-attachments/assets/344667fe-b937-41da-a6fb-7edcb2346f02" />

## OUTPUT (HPF) : 
<img width="759" height="322" alt="image" src="https://github.com/user-attachments/assets/6307607d-13c6-44ea-9fd9-b4d48817626e" />

## RESULT: 
Thus, the IIR Butterworth Low Pass Filter (LPF) and High Pass Filter (HPF) were plotted and output was verified.
