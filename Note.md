<img width="1207" height="760" alt="image" src="https://github.com/user-attachments/assets/1ea59e47-f98e-414f-8bea-05942b6a8e20" />


## Timing Error Detector (TED)
The *input* of TED is the *output* of interpolator ($z(mT_M+\epsilon_\Delta)$, the output of matched filter), the output is the $e_D[m]$. How to find out the $e_D[m]$?
1. use the $|z(nT_s)|^2$ instead of regular $z(nT_s)$. why is that?\
   1.1 We may find out that $z(nT_s)$ is actually random, and the $|z(nT_s)|^2$ exhibits some traits of sinusoid (like the period of $T_M$).
   <img width="706" height="257" alt="image" src="https://github.com/user-attachments/assets/3b619292-54de-4a52-9f80-746fd806f21c" />
2. Using the derivate of $|z(nT_s)|^2$ to find out the maximum eye open.

Then use the law of derivative, the output of TED can be represented as 
$e_D[m]=z(mT_M+\epsilon_\Delta) \cdot z'(mT_M+\epsilon_\Delta)$, as the $|z(nT_s)|^2$ get its maximum, the derivative goes to zero.
While there is no specific math prove, but according to the diagram of matched filter output($z(nT_s)$), it illustrates that it really is a way.
### More details of Derivative TED
In this book, it suggests that no need to really compute the derivate of $z(mT_M+\epsilon_\Delta)$, while use the pre-computed derivative of matched filter.
> hint: the derivate can be implemented as $h(nTs)=\frac{1}{2}$\{1,0,-1\}
> so\
> $z'(nTs)=z(nTs)*h(nTs)$ = $r(nTs)\*p(-nTs)\*h(nTs)$ = $r(nTs)\*$ \{ $p(-nTs)\*h(nTs)$ \} \
> \{ $p(-nTs)\*h(nTs)$ \} is called the Timing Matched filter $h_{TMF}(nTs)$

The next question is how to evaluate the performance of the TED? The mean curve comes to play. Why use mean curve not the one instance? Basically because the average of the noise will be 0,\
help to converge.

Mean \{ $e_D[m]$ \} = $\bar{e_D}$, which $\epsilon_\Delta$ - $\hat{\epsilon_\Delta}$ = $\epsilon_{\Delta\:e}$.\
It also can be represented as 
> $\bar{e_D}$  = $- \gamma A^2 \cdot {r_p}'(\epsilon_{\Delta\:e})$.

However, the larger the positive slope at $\epsilon_{\Delta\:e} = 0$, the faster it will converge to 0, but the self noise will affect the accuracy, so we use

> $\frac{squared\ slope\ of\ the\ mean\ curve\ at\ \epsilon_{\Delta\:e} = 0}{error\ variance\ at\ \epsilon_{\Delta\:e} = 0}$

### Drawback of derivative TED
There will be more samples to exchange for the accuracy of TED, while in practical, the L(samples per symbol) is limited and preferred small, so how to replace the derivative for simpler implementation is needed.

### Early-late TED
The phrase "early-late" just means the samples in reference to the current symbol m.\
Make L=2 samples/symbol, the samples we use is :
> $z((m-1)T_M+\hat{\epsilon_\Delta})$ , $z(mT_M-\frac{T_M}{2}+\hat{\epsilon_\Delta})$ , $z(mT_M+\hat{\epsilon_\Delta})$ , $z(mT_M+\frac{T_M}{2}+\hat{\epsilon_\Delta})$ , $z((m+1)T_M+\hat{\epsilon_\Delta})$

In simple words, \
in the current symbol is m, which is $z(mT_M+\hat{\epsilon_\Delta})$ \
the previous symbol is m-1, which is $z((m-1)T_M+\hat{\epsilon_\Delta})$ \
the later symbol is m+1, which is $z((m+1)T_M+\hat{\epsilon_\Delta})$ \ 
In the between, there are $z(mT_M-\frac{T_M}{2}+\hat{\epsilon_\Delta})$ and $z(mT_M+\frac{T_M}{2}+\hat{\epsilon_\Delta})$.\
Combining with the derivative filter $h[n]$ 
> $h[n] = \{ +1,0,-1 \}$, here we ignore the $\frac{1}{2}$ to simplify.

So for the current symbol m, we focus on 3 sample :
> current: $z(mT_M+\hat{\epsilon_\Delta})$ 
> early: $z(mT_M-\frac{T_M}{2}+\hat{\epsilon_\Delta})$ 
> late:  $z(mT_M+\frac{T_M}{2}+\hat{\epsilon_\Delta})$

The Output can be revised as :\
$e_D[m]$ = $z(mT_M+\hat{\epsilon_\Delta})$ { $z(mT_M+\frac{T_M}{2}+\hat{\epsilon_\Delta})$ - $z(mT_M-\frac{T_M}{2}+\hat{\epsilon_\Delta})$ } 

<img width="976" height="806" alt="image" src="https://github.com/user-attachments/assets/e132b94a-ab2e-4935-90fb-d3f3420210d5" />

So instead of sampling the $z(mT_M+\hat{\epsilon_\Delta})$, The Interpolator block samples at $z(nT_s+\hat{\epsilon_\Delta})$, so TED can delay the sample to implement the early-late derivative TED methodology.\
From previous formula, the output of TED is :
> $\bar{e_D}$  = $- \gamma A^2 \cdot {r_p}'(\epsilon_{\Delta\:e})$.

The Output of early-late TED can be revised as:
> $\bar{e_D}$  = $- \gamma A^2 \cdot {r_p}'(\epsilon_{\Delta\:e})$ = $\gamma A^2$ { $r_p(\frac{T_M}{2}-\epsilon_{\Delta\:e}) - r_p(-\frac{T_M}{2}-\epsilon_{\Delta\:e})$ }

### Important notes !!!!
How to know which sample is the correct representation of symbol m? 
<img width="805" height="431" alt="image" src="https://github.com/user-attachments/assets/67388147-bf6f-40ee-be75-31512dd2e86b" />

If we choose c as the symbol m, then 
> $e_D[m]=c(b-d) > 0$

which implies that the estimated $\hat{\epsilon_\Delta}$ $<$ actual $\epsilon_\Delta$, which also means it samples too early(not delay enough), and next time it will drive the $z(mT_M+\hat{\epsilon_\Delta})$ more to the right.
> $e_D[m]=d(c-e) < 0$

which implies that the estimated $\hat{\epsilon_\Delta}$ $>$ actual $\epsilon_\Delta$, which also means it samples too late(delay too enough), and next time it will drive the $z(mT_M+\hat{\epsilon_\Delta})$ more to the left.\



## Interpolator block

The major jobs of Interpolator block (IB) are:
1. find out the time delay $\epsilon_\Delta$ and
2. find out which sample (nTs) is the basic index for maximum eye open.

