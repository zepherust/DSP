<img width="1207" height="760" alt="image" src="https://github.com/user-attachments/assets/1ea59e47-f98e-414f-8bea-05942b6a8e20" />
NOTES FROM QUASIM CHAUDHARI "Wireless Com"

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
One question I ask myself: in the beginning chart, there are some samples say nTs, and the output is $z(mT_M+\hat{\epsilon_\Delta})$, how the interpolator know what is the value between these samples?\
<img width="770" height="256" alt="image" src="https://github.com/user-attachments/assets/b54971b6-c424-4f55-bab8-9819597562c1" />

>The input samples are 🔵 BLUE dots, while we need to know the ◯ dot.

The major jobs of Interpolator block (IB) are:
1. find out the time delay $\epsilon_\Delta$ and
2. find out which sample (nTs) is the basic index for maximum eye open.

### Polynomial Interpolation
With the help of near samples, we first assume that the matched filter output $z(nT_s)$ is waveform of $z(t)$
> $z(t) \approx c_p t^p + c_{p-1} t^{p-1} + ... + c_1 t + c_0$

#### Linear Interpolation, p =1
> $z(t) \approx c_1 t + c_0$\
> $z(nT_s) = c_1 (nTs) + c_0$\
> $z((n+1)T_s) = c_1 ((n+1)Ts) + c_0$

two coefficients $c0,c1$ and two equations will solve them.\
with the help of two coefficents, the final target 
> $z(nT_s+\mu _{m}T_s) = \mu _{m} z((n+1)T_s) + (1-\mu _{m}) z(nT_s)$ 

#### Cubic Interpolation, p =3
Like Linear interpolation, we just give the final target result:
> $z(nT_s+\mu _{m}T_s) = (\frac{{\mu _{m}}^3}{6} - \frac{\mu _{m}}{6}) z((n+2)T_s) +$\
>                        $(-\frac{{\mu _{m}}^3}{2}+\frac{{\mu _{m}}^2}{2} + \mu _{m})z((n+1)T_s) +$\
>                        $(\frac{{\mu _{m}}^3}{2}-{\mu _{m}}^2 - \frac{\mu _{m}}{2} + 1)z(nT_s) +$\
>                        $(-\frac{{\mu _{m}}^3}{6}+\frac{{\mu _{m}}^2}{2} - \frac{\mu _{m}}{3})z((n-1)T_s)$

#### Quadratic Interpolation, p =2
> hint: if we use only 3 samples, then how we can differentiate the middle sample? like between n and n+1, the formula will be:\
> $z(nT_s+\mu _{m}T_s) = (\frac{{\mu _{m}}^2-\mu _{m}}{2})z((n-1)T_s) + (1-{\mu _{m}}^2)z(nTs) + (\frac{{\mu _{m}}^2+\mu _{m}}{2}) z((n+1)T_s)$\
> After searching on some AI, that say how to represent the middle point  $z(nT_s+\mu _{m}T_s)$ is not important, but the derivate of mid sample just ignore the left part.\
> ${\color{red}BUT\ NOW\ I\ STILL\ NOT\ GET\ IT.\ WILL\ UPDATES\ WHEN\ I\ GET\ IT.\ TODO}$

another way is to add another sample n+2, it will solve the problem.

#### Generation Interpolation, p = $\infty$
we set the $\mu _m$ related coefficients as the values of $h[n]$. we can generalize the above polynomial interpolation as $h[n]$ convoluted with the $z(nTs)$ to get the target $z(nT_s+\mu _m)$.
> Hint: keep in mind that $h[n]$ is just the discrete presentation of underlying continious $h(t)$ filter.\
> $h[-1]$ is actually the sample at the point $t=-1+\mu _m$.

In other words, if we increase the $\mu _m$ from 0 to 1, then the $h[-1]$ is correlated to the $z((n+1))T_s$ should be increased from 0 to 1. The more polynomial, it will becomes sinc function.

