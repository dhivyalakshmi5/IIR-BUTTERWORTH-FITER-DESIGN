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
## PROGRAM (BPF): 
```
clc;
clear;
close;

// ---- Specifications ----
wp1 = %pi/4;     // Lower passband edge
wp2 = %pi/2;     // Upper passband edge
ws1 = %pi/6;     // Lower stopband edge
ws2 = 2*%pi/3;   // Upper stopband edge

alphap = 1;      // Passband attenuation (dB)
alphas = 15;     // Stopband attenuation (dB)
T = 1;

// ---- Prewarping ----
omegap1 = (2/T)*tan(wp1/2);
omegap2 = (2/T)*tan(wp2/2);
omegas1 = (2/T)*tan(ws1/2);
omegas2 = (2/T)*tan(ws2/2);

// ---- Band parameters ----
B = omegap2 - omegap1;
omega0 = sqrt(omegap1 * omegap2);

// ---- Convert to LPF equivalent ----
Omega_p = 1;
Omega_s = min(abs((omegas1^2 - omega0^2)/(B*omegas1)), ...
              abs((omegas2^2 - omega0^2)/(B*omegas2)));

// ---- Filter Order ----
N = log10((10^(0.1*alphas)-1)/(10^(0.1*alphap)-1)) / (2*log10(Omega_s/Omega_p));
N = ceil(N);

// ---- Cutoff frequency ----
omegac = 1 / ((10^(0.1*alphap)-1)^(1/(2*N)));

// ---- Analog LPF prototype ----
hs = analpf(N, 'butt', [0,0], omegac);

// ---- LPF → BPF transformation ----
s = poly(0, 's');
Hs_bp = horner(hs, (s^2 + omega0^2)/(B*s));

// ---- Bilinear transformation ----
z = poly(0, 'z');
Hz = horner(Hs_bp, (2/T)*((z-1)/(z+1)));

// ---- Frequency response ----
HW = frmag(Hz, 512);
w = 0:%pi/511:%pi;

// ---- Plot ----
plot(w/%pi, abs(HW));
xlabel('Normalized Frequency (×π rad/sample)');
ylabel('Magnitude');
title('Butterworth IIR Band-Pass Filter');

// ---- Display ----
disp("Filter Order N = "), disp(N);
disp("Center Frequency omega0 = "), disp(omega0);
disp("Bandwidth B = "), disp(B);
disp("Transfer Function H(z) = "), disp(Hz);

```
## PROGRAM (BSF): 
```
clc;
clear;
close;

// ---- Specifications ----
wp1 = %pi/6;      // Lower passband edge
wp2 = 2*%pi/3;    // Upper passband edge
ws1 = %pi/4;      // Lower stopband edge
ws2 = %pi/2;      // Upper stopband edge

alphap = 1;       // Passband attenuation (dB)
alphas = 15;      // Stopband attenuation (dB)
T = 1;

// ---- Prewarping ----
omegap1 = (2/T)*tan(wp1/2);
omegap2 = (2/T)*tan(wp2/2);
omegas1 = (2/T)*tan(ws1/2);
omegas2 = (2/T)*tan(ws2/2);

// ---- Band parameters ----
B = omegap2 - omegap1;
omega0 = sqrt(omegap1 * omegap2);

// ---- Convert to LPF equivalent ----
Omega_p = 1;
Omega_s = min(abs((B*omegas1)/(omegas1^2 - omega0^2)), ...
              abs((B*omegas2)/(omegas2^2 - omega0^2)));

// ---- Filter Order ----
N = log10((10^(0.1*alphas)-1)/(10^(0.1*alphap)-1)) / (2*log10(Omega_s/Omega_p));
N = ceil(N);

// ---- Cutoff frequency ----
omegac = 1 / ((10^(0.1*alphap)-1)^(1/(2*N)));

// ---- Analog LPF prototype ----
hs = analpf(N, 'butt', [0,0], omegac);

// ---- LPF → BSF transformation ----
s = poly(0, 's');
Hs_bs = horner(hs, (B*s)/(s^2 + omega0^2));

// ---- Bilinear transformation ----
z = poly(0, 'z');
Hz = horner(Hs_bs, (2/T)*((z-1)/(z+1)));

// ---- Frequency response ----
HW = frmag(Hz, 512);
w = 0:%pi/511:%pi;

// ---- Plot ----
plot(w/%pi, abs(HW));
xlabel('Normalized Frequency (×π rad/sample)');
ylabel('Magnitude');
title('Butterworth IIR Band-Stop Filter');

// ---- Display ----
disp("Filter Order N = "), disp(N);
disp("Center Frequency omega0 = "), disp(omega0);
disp("Bandwidth B = "), disp(B);
disp("Transfer Function H(z) = "), disp(Hz);
```

## OUTPUT (LPF) : 
<img width="763" height="323" alt="image" src="https://github.com/user-attachments/assets/344667fe-b937-41da-a6fb-7edcb2346f02" />

## OUTPUT (HPF) : 
<img width="759" height="322" alt="image" src="https://github.com/user-attachments/assets/6307607d-13c6-44ea-9fd9-b4d48817626e" />

## OUTPUT (BPF) : 

![WhatsApp Image 2026-03-28 at 11 23 26 AM](https://github.com/user-attachments/assets/bcef2c2a-df2a-4b34-99f4-e66969d3f0f7)


## OUTPUT (BSF) : 
<img width="1600" height="587" alt="image" src="https://github.com/user-attachments/assets/202b3d77-bd85-45e6-bba8-ed68dd5e15b8" />

## RESULT: 
Thus, the IIR Butterworth Low Pass Filter (LPF) and High Pass Filter (HPF) were plotted and output was verified.
