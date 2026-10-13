# Overlooks in DSP
> why I create THIS named "Overlooks" cause in real implementations combining with some DSP related
> books and papers, there are MANY things I thought they are just come by nature but actually not
>

## The sinc function
Talking about sinc function, we are all familiar with, is window function in another domain.
Why this coming first cause when I actually design a down sample filter, there are many things
need to be dealed with, and when I look back the knowledge or axioms, which are nature and easy
understanding, contains many fruits of wisdom which I admire and feel brilliant.

### Infinity vs Finity
In real implementation, there are impossible we can implement the infinite filter $h(t)$ is 
continuous in time domain. So as for that, the another domain is not a perfect window function.
Things are often overlooked by me is that \
1 how to choose how many samples(or taps) of this discrete time $h[n]$? \
2 Why this filter works and how to expand this utilities?\

#### how to choose how many samples(or taps) of this discrete time $h[n]$?
Back in school days or the beginning period of my DSP career, we just been giving a filter and 
use it for the real "important" questions, like how to down sample, how to get the correlation,
how to calculate the frequency offset. I often overlook how to get this filter when the day
I implement the first down sample filter which need to down sample the time domain signal.
After research, according to the Kaiser window methodology (ofcourse there are many other window
methods, I just choose one of them to research):
> $h[n] = h_{ideal}[n] \cdot w_{Kaiser}[n]$ \
> $w_{Kaiser}[n] = \frac{I_0 \( \beta \sqrt{ 1 - \( \frac{2n}{N-1} - 1 \)^2} \) }{T_0 \( \beta \)}$ \
> where 
> $I_0\( \cdot \)$ is the  zeroth-order modified Bessel function of the first kind (actually I still duno
> know what this is, but it will not stop my understanding from filter building) 
> $\beta$ is the parameter control the sidelobe level, in other words it control the decay or the signal you do not want.
> $N$ clearly is the number of taps.

Another parameter need to be justify or verify the performance of a filter is the stopband attenuation.
> $A = -20 log_{10} \( \delta_{s} \)$ 
> $\delta_{s}$ is the linear stopband ripple. Smaller the better. 

#### Why this filter works and how to expand this utilities??
The question will be more specific and break down into 2 small questions:\
1 How to actually design a filter that works?\
2 How to make this filter or window works in another field or questions? 

##### How to actually design a filter that works?
The basic principle of design a filter can be in 3 steps:
1. compute $\beta$
```math
\beta = \left\lbrace
\begin{array}{ll}
0.1102(A - 8.7), & A > 50 \\[4pt]
0.5842(A - 21)^{0.4} + 0.07886(A - 21), & 21 \le A \le 50 \\[4pt]
0, & A < 21
\end{array}
\right.
```
2. Compute the filter length N  (but in reality, choose N I think will be more important in some way,
   cause I need to evaluate the performance of physical device.)\
   $N \approx \frac{A-8}{2.285\Delta w}$
3. Compute the exact tap corresponding values.\
   $h_{ideal}[n] = \frac{sin(w_c\(n-\(N-1\)/2\)\)}{\pi \( n-\( N-1 \)/2 \)}$

For me, nothing will work better than a numeric example.
1. $F_s = 30.72Mhz$
2. Need to down sample the signal by M=4, which the new Nyquist sample frequency is $\frac{F_s}{2M} = 3.84Mhz $
3. signal bandwidth, the max signal frequency you want to keep, $f_p = 5Mhz/2 = 2.5Mhz$.
4. So the $\Delta f = 3.84-2.5 = 1.34Mhz $, and the $\Delta w = 2 \pi \frac{\Delta f}{F_s} = 0.2741 rad/sample$
5. If I choose $N=15$ taps, then the $A$ will be $2.285 \Delta w N + 8 \approx 18dB$, which is poor but usable in my scenario.
6. So the stop band must start at $f_{st} = 3.84Mhz$, the $f_c = \frac{f_p + f_{st}}{2} = 3.17Mhz$
7. The ideal sinc function at cutoff $w_c = 2 \pi f_c / F_s = 2 \pi \frac{3.17}{30.72} = 0.6484$
8. The ideal sinc function is $h_{ideal}[n] = \frac{sin(w_c\(n-\(N-1\)/2\)\)}{\pi \( n-\( N-1 \)/2 \)}$, which $w_c$ is known now.
9. There will be plenty of resouce on this version of internet help us to find the numeric value of kaiser value.
10. Finally, I got my desired Filter, which is not perfect I know, but what it taught me is perfect :)


