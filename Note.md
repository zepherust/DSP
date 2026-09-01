<img width="1207" height="760" alt="image" src="https://github.com/user-attachments/assets/1ea59e47-f98e-414f-8bea-05942b6a8e20" />

## Interpolator block

The major jobs of Interpolator block (IB) are:
1. find out the time delay $\epsilon_\Delta$ and
2. find out which sample (nTs) is the basic index for maximum eye open.

## Timing Error Detector (TED)
The *input* of TED is the *output* of interpolator ($z(mT_M+\epsilon_\Delta)$, the output of matched filter), the output is the $e_D[m]$. How to find out the $e_D[m]$?
1. use the $|z(nT_s)|^2$ instead of regular $z(nT_s)$. why is that?
   1.1 We may find out that $z(nT_s)$ is actually random, and the $|z(nT_s)|^2$ exhibits some traits of sinusoid (like the period of $T_M$).
   <img width="706" height="257" alt="image" src="https://github.com/user-attachments/assets/3b619292-54de-4a52-9f80-746fd806f21c" />
2. Using the derivate of $|z(nT_s)|^2$ to find out the maximum eye open.

Then use the law of derivative, the output of TED can be represented as 
$e_D[m]=z(mT_M+\epsilon_\Delta) \cdot z'(mT_M+\epsilon_\Delta)$, as the $|z(nT_s)|^2$ get its maximum, the derivative goes to zero.
While there is no specific math prove, but according to the diagram of matched filter output($z(nT_s)$), it illustrates that it really is a way.
