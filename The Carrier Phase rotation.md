> First of all, why I write this notes because the issue torture me like 1 MONTH to get the right signal!!

### The CFO estimation in FDMA

I get into this topic in my early career of DSP processing, and I also implement a method of CFO estimation and compensation base on
this [paper](https://eudl.eu/pdf/10.1007/978-3-642-29157-9_25), basically, it use the difference of 2 DMRS symbols
channel estimation, so the maximum range of this method estimation is 
> from $cos( 2\pi \Delta f \Delta t )$ + $j sin(2\pi \Delta f \Delta t )$
> since the rad is $0 \le 2\pi \Delta f \Delta t \le 2\pi$
> the maximum cfo is $\le \frac{1}{\Delta t} = \frac{1}{\frac{1ms}{2^u} \cdot \frac{symbol_d}{symbols}}$
> in LTE, 1ms = 1 subframe, so the maximum is 2000HZ based on the 2 dmrs symbol difference is 7
> If the DMRS symbol difference is 1, then the maximum is 14K Hz (the real maximum but can not be implemented in LTE since the dmrs location is fixed)

Why I mention it cause what I encounter in real signal from SMW or VSG, the received signal maybe distorted by MANY things, so First of first, I realize that the original method can not solve me problem since the actual CFO is out of range by DMRS method. What I finally used is the real maximum range: the expected data in data location(not the dmrs location). Refer to the method in previous paper, replace the dmrs with data, and get the approximately 6kHZ cfo(You may think this way is easy to recall, but I need to check the instruments configuration setting, the synchronization problem). Now I can located the problem is on the RRU CFO synch problem, and finally decoded the desired payload.
