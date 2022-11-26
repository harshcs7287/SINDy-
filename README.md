# Data-driven Identification of Governing Equation using SINDy Algorithm

## Motivation

Knowledge of Governing equation in the dynamic system is very important but in several cases the first principle based equations may not be available for the system.
So, it becomes necessary to develop an understanding of the system using data-driven methods.

In this work, I will try to identify the system dynamics of Continuous Stirred Tank Reactor (CSTR).

The continuous stirred-tank reactor (CSTR) is a common model for a chemical reactor in chemical engineering and environmental engineering.

Integral mass balance on number of moles N_A of species A in a reactor of volume V:

![alt text](https://wikimedia.org/api/rest_v1/media/math/render/svg/8eebf2350ec13c737ba7a73c8d785bbd0304bcb6.png)


## Training Data

```Matlab

function [z_dot]= function_deri(t,z)
M=z(1);
P1=z(2);
P2=z(3);
P3=z(4);


% M --> P1           initiation step
% P1 + M --> P2      propagation step
% P2 + P1--> P3      terminal step

Fin=1;Fout=1;V=10;CMin=1;Ki=2;Kp=2;Kt=5;
z_dot(1,1)=Fin*CMin-Fout*M + V*(-Ki*M - Kp*M*(P1));
z_dot(2,1)=-Fout*P1 + V*(Ki*M - Kp*M*P1);
z_dot(3,1)=-Fout*P2 + V*(Ki*M*P1 - Kp*P1*P2);
z_dot(4,1)=-Fout*P3 + V*(Kt*P1*P2);

end
```
