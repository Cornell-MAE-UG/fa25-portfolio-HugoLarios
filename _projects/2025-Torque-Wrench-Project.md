---
layout: project
title: Torque Wrench Design
description: Advanced CAD Project
technologies: [Autodesk Fusion, MATLAB, ANSYS]
image: /assets/images/torque-wrench.jpg
---


### Summary
For the final project of MAE 3270: Mechanics of Materials, we were tasked with designing a simple torque wrench that could perform under specified parameters (static loading, fatigue, and fracture strength). After selecting the design and material that the torque wrench was made of, I needed to create a CAD and run an FEM over this model using ANSYS Static Structural. Comparing the analysis from ANSYS to my own hand calculations, I better understood the assumptions that had to be made to properly model the wrench.

---

### Designing the wrench:

When beginning the preliminary design of the torque wrench, we were given the following problem statement:

"Your assignment is to design a non-ratcheting, 3/8 inch drive instrumented torque wrench rated for 600 in-lbf. Torque will be transduced using strain gauges bonded to the outer surfaces of the wrench at high strain locations. I am also asking you to perform a finite element analysis of your final design.
The torque will be transduced by strain gauges on the sides of the torque wrench. The design goal is to maximize the voltage output of the wrench (mV/V) at the rated torque. The design is required to attain at least 1.0 mV/V output at the rated torque of 600 in-lbf. Higher output will lead to more sensitivity and improved signal to noise ratio. The constrains are that the wrench must not fail due to static loading, crack growth or fatigue. Your design will include selecting an appropriate material and dimensions to meet or exceed the following requirements:
- 1) Attain at least 1.0 mV/V output at the rated torque of 600 in-lbf.
- 2) Safety factor of X_0 = 4 for yield or brittle failure.
- 3) Safety factor of X_k = 2 for crack growth from an assumed crack of depth 0.04 inches (1 mm).
- 4) Fatigue stress safety factor of X_s = 1.5.
- 5) Material must be a steel, aluminum, or titanium alloy."

With these design considerations in mind, I first drew out the parameters and necessary equations within my notebook, before placing these equations into a MATLAB script. I first tested a steel alloy (M42 Steel), before moving onto a titanium alloy (Ti-6Al-4V).

---

![Notebook calculations]({{ "/assets/images/Notebook-Materials.jpeg" | relative_url }}){: .inline-image-r style="width: 50%"}

---

```matlab
    b = 0.6;    h = 0.5;   L = 16;  % Dimensions of wrench (inches)
    c = 1; % Location of strain gauge (inches)
    X_o_in = 4;    X_k_in = 2;    X_s_in = 1.5; % Required Factors of Safety
    M_max = 600; % Maximum allowable moment (lbf*in)
    P = M_max / L; % Reaction force used for bending (lbf)
    a = 0.04; % Crack length (inches)

    % M42 Steel 
    % E = 32e+06; % Young's modulus (psi)
    % stress_yield = 370e+03; % Yield strength (psi)
    % KIC = 15e+03; % Fracture toughness (psi*sqrt(in))
    % s_fatigue = 115e+03; % Fatigue strength for 10^6 cycles (psi)

    % Ti-6Al-4V
    E = 16.3e+06;   % Young's modulus (psi)
    stress_yield = 114e+03;  % Yield strength (psi)
    KIC = 94.2e+03; % Fracture toughness (psi*sqrt(in))
    s_fatigue = 88.9e+03; % Fatigue strength for 10^6 cycles (psi)

    % Static Loading
    X_o = (stress_yield*b*(h^2)) / (M_max*6) % Static Factor of Safety

    Max_normal_stress = (M_max) / (b*h^2 / 6) % Maximum normal stress

    u_max = (M_max*L^2) / (3*E*b*h^3 / 12) % Max deflection (inches)

    % Strain gauge calculations after bend
    M_gauge = P*(L-c); % Moment felt at strain gauge
    sigma_g = 6 * M_gauge / (b*h^2); % Stress felt at strain gauge

    eps_g = sigma_g / E; % Calculation of microstrain
    out = 1000 * eps_g % Conversion to mV/V

    % Fracture Loading Factor of Safety
    X_k = (KIC / M_max) * (b*h^2 / 6) * (1/(1.12*sqrt(pi*a)))

    % Fatigue Loading Factor of Safety
    X_s = (b*h^2 / 6) * (s_fatigue / M_max)

```

---

### CAD Drawing

Below is the full drawing of my final CAD model. The critical dimensions for manufacturing and FEM modeling are listed. 
---

![Fusion 360 Drawing]({{ "/assets/images/Torque_Wrench_Drawing.jpg" | relative_url }}){: .inline-image-r style="width: 50%"}

---

### Material & Material Properties

The wrench is fabricated from Ti-6Al-4V, which best fits the material strain requirement.
The key properties include:
- Young's Modulus: 16.3*10^6 psi
- Poisson's Ratio: 0.332
- Yield Strength: 114 ksi
- Fracture Toughness: 94.2 ksi*sqrt(in)
- Fatigue Strength (10^6 Cycles): 88.9 ksi
- Density: 4.43 g/cm^3 (not structurally relevant but included for completeness)

---

### FEM Load and Boundary Condition Diagram

The FEM setup followed the course instructions and is illustrated below:
- The top 0.4 inches of the drive block was fully constrained (zero displacement, shown in yellow).
- A lateral force was applied on the wrench to create a net torque around the drive block. This force was F= 600/16 = 37.5 lbf.

![Force FEM]({{ "/assets/images/Force.png" | relative_url }}){: .inline-image-l}

---

### Normal Strain Contours (Gauge Direction)

Below is the strain distribution along the gauge axis. The peak gauge-direction strain from the probe was 2585.3 microstrain.

![Normal Elastic Strain FEM]({{ "/assets/images/Normal-Elastic-Strain.png" | relative_url }}){: .inline-image-l}
