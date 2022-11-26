# Data-driven Identification of Governing Equation using SINDy Algorithm in a CSTR

## Motivation

Knowledge of Governing equation in the dynamic system is very important but in several cases the first principle based equations may not be available for the system.
So, it becomes necessary to develop an understanding of the system using data-driven methods.

In this work, I will try to identify the system dynamics of Continuous Stirred Tank Reactor (CSTR) using the time-concentration data.

## Continuous Stirred Tank Reactor (CSTR)

The continuous stirred-tank reactor (CSTR) is a common model for a chemical reactor in chemical engineering and environmental engineering.

Integral mass balance on number of moles of species A in a reactor of volume V:

![alt text](https://github.com/harshcs7287/SINDy-/blob/main/mass_balance.jpg?raw=true)

## Sparse Identification of Non-linear Dynamics

Sparse regression is a machine learning methodology which works under the assumption that the governing equations
of most dynamical systems contain very few terms. These equation can be considered sparse in the function space and
the system is expected to evolve on a low dimensional manifold. Sparse Identification tries to discover these equations
from noisy time series data.


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
```Matlab

clc
z0=[1;0;0;0];
[tval,zval]=ode45(@(t,z) function_deri(t,z),[0:0.01:0.5],z0);
plot(tval,[zval(:,1) zval(:,2) zval(:,3) zval(:,4)])

zval_new=zval([1:2:101],:)
tval_new=tval([1:2:101]);
zder_val=diff_fun(zval_new,tval_new);
```

![alt text](https://github.com/harshcs7287/SINDy-/blob/main/Concentration_Graph.jpg?raw=true)

## Deply SINDy Algorithm

```Matlab
function [d_oa] = diff_fun(zval,tval)
[m , n]=size(zval);

   d_oa=zeros(m-1,n);   
   d_tval=diff(tval);
   for i=1:n
       d_zval=diff(zval(:,i));
       d_oa(:,i)=(d_zval)./(d_tval);
   end
end
```

```Matlab
zval_mod=zval(2:end,:)
M=zval_mod(:,1);
P1=zval_mod(:,2);
P2=zval_mod(:,3);
P3=zval_mod(:,1);
X=[ones(size(zval_mod,1),1) , zval_mod , zval_mod.^2 M.*( zval_mod(:,2:end))
P1.*(zval_mod(:,3:end)) P2.*P3];
[B,S] = lasso(X,zder_val(:,1),'CV',3);
theta_opt = (B(:,S.IndexMinMSE));
```
theta_opt = [



