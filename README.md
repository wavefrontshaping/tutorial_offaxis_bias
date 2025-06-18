# How to correct for bias in off-axis holography for transmission matrix measurements

I presented in a previous [tutorial](https://www.wavefrontshaping.net/post/id/12) how to reconstruct a complex field using a camera and a plane wave reference tilted with respect to the optical axis. This works perfeclty with an ideal plane wave as a reference. Of course, real life is not perfect and the reference usually present imperfections. While low spatial fluctuations can be compensated afterwards, high spatial frequency noise has the effect of adding a small bias to the estimation of the field. Such bias is typically small, but in transmission matrix measurements, as it is static and added to all the measurements, it can affect the singular value distribution and perturb or hide transmission channels that would otherwise be visible.

## Off-axis holography process (reminder)

Let \(E_s(\vec{r})\) be the wavefront we want to characterize and \(E_\text{ref}(\vec{r}) e^{i\vec{\nu}.\vec{r}}\) the field used as reference, with \(\vec{\nu}\) representing the spatial frequency of the interference fringes in the plane of the camera (\(\vec{\nu}.\vec{r} = \nu_x x + \nu_y y\)).
In the ideal case, \(E_\text{ref}(\vec{r}) = E_0\) is constant (i.e. representing a plane wave). Thus, for a well chosen angle between the reference and the signal, representend by \(\vec{\nu}\), the interference pattern can be divided into terms well separated in the Fourier plane:

$$ I=\underbrace{\left|E_0\right|^2+\left|E_s(\vec{r})\right|^2}_{\textrm{order 0}}+\underbrace{E_0 E_s^*(\vec{r})e^{ik \vec{\nu}.\vec{r}}}_{\textrm{order +1}}+ \underbrace{E_0^* E_s(\vec{r})e^{-ik \vec{\nu}.\vec{r}}}_{\textrm{order -1}}$$

The process, explained in more details in this [tutorial](https://www.wavefrontshaping.net/post/id/12), can be devided in


1. Fourier tranformal of the interferogram, written as  \(F\),
2. Filtering to keep the window of spatial frequencies centered around the desired order, written as \(Filter\),
3. Shift the signal around the DC compoennt (zero spatial frequency), noted as $Shift$,
3. Inverse Fourier transform, noted as \(F^{-1}\).

The goal of \(Filter\) is to keep only the -1 order and having it centered around the 0 frequency,  so that 

$$ 
OA\left[I\right] = F^{-1}\left[Shift\left[Filter\left[F\left[I\right]\right]\right]\right] = E_0^* E_s(\vec{r}) \propto E_s(\vec{r})
$$

So the operator $OA$ representing the off-axis operation is the composition of those four operations and reads:
$$ 
OA = F^{-1} \circ Shift \circ Filter \circ F
$$

Note that we assume that everything is OK, meaning that we \(\vec{\nu}\) is chosen so that: 
- the fringes of $I$ are well sampled by the pixels of the camera, so that we can disregard the effect of the sampling and just consider the continuous variable \(\vec{r}\),
- the \(\|\vec{\nu}\|\) is higher than the maximal spatial frequency of the signal, so that orders \(\pm 1\) do not overlap with the O-th order.

## Effect of imperfect reference 

In a real experiment, \(E_\text{ref}(\vec{r})\) is not constant but present fluctuations that we will consider random. We can devide those in two terms, high- and low-spatial frequency compenents, we will define those term a bit more precisely in the following:

$$E_\text{ref}(\vec{r}) = E_0+E_{LF}(\vec{r})+E_{HF}(\vec{r})$$

We will assume here that the phase of the reference (without the off-axis term) is still constant, so that all those terms are real. 

We should no consider the intensity of the interferences 

$$ I=\underbrace{\left|E_\text{ref}\right|^2+\left|E_s(\vec{r})\right|^2}_{\textrm{order 0}}+\underbrace{E_\text{ref} E_s^*(\vec{r})e^{ik \vec{\nu}.\vec{r}}}_{\textrm{order +1}}+ \underbrace{E_\text{ref}^* E_s(\vec{r})e^{-ik \vec{\nu}.\vec{r}}}_{\textrm{order -1}}$$

### Effect of low-spatial frequency fluctuations

In the presence of only loow spatial frequency fluctuations

$$OA\left[I\right] = \left[E_0+E_{LF}(\vec{r})\right] E_s(\vec{r}) $$

In essence, that means that low spatial frequencies of the reference, i.e. frequencies terms that are not filtered-out by the \(Filter\) operation, are modulating the mesaured signal.
This term can be easily compensated for by mesuring the intensity of reference, filtering it to conserve only low spatial frequencies (corresponding to the mask in the Fourier plane we applied in the off-axis procedure), and divide the result of the off-axis operation by this term. 

Thus, the signal field is approximated by \(\hat{E}_s(\vec{r})\) that is computed using

$$
\hat{E}_s(\vec{r}) = OA\left[I\right] / B(\vec{r})
$$

with 
$$
B(\vec{r}) = F^{-1} \circ Filter \circ F\left[I^\text{ref}(\vec{r})\right]
$$

Note that we apply here on the reference intensity only $I^\text{ref}(\vec{r})$ the same operation as for the off-axis $OA$ but **without** the shift operation, as we are impacted by low spatial frequencies of $I^\text{ref}$. 

### Effect of high-spatial frequency fluctuations

If we now consider only the high-spatial frequency fluctuations, the problem is slightly different. In the cross term we identified as the -1 order, those terms will be filtered out by \(Filter\). However, the term corresponding to the intensity of the reference \(\left|E_\text{ref}\right|^2\) may overlap with the \(\pm 1\) orders.


$$ F^{-1}\left[Filter\left[F\left[I\right]\right]\right] = E_0(\vec{r}) E_s(\vec{r}) + I^\text{ref}_{HF}(\vec{r}) \approx
E_0(\vec{r}) E_s(\vec{r}) + \left| E_{HF}(\vec{r})\right|^2$$

So we had a bias \(I^\text{ref}_{HF}(\vec{r})\) to all the estimations of the field corresponding to the high spatial frequencies of the intensity of the reference.
To be more precise, it correspond to spatial frequencies centered around \(\vec{\nu}\) and with a bandwidith corresponding to the \(Filter\) operation. However, it is not necessary to go into those details to recover and remove this term.
Indeed, this term is the one, and the only one, that appears if one apply the full off-axis operation to the intensity of the reference.

$$ F^{-1} \circ Filter \circ Shift \circ F\left[I^\text{ref}\right] = I^\text{ref}_{HF}(\vec{r}) $$

Thus, in practice, it is quite simple to obtain. We first block the signal beam to only record the intensity of the reference, and then apply the off-axis operation to it. The results should then be removed to all subsequent measurement with the signal.

A better estimation \(\hat{E}_s(\vec{r})\) of the signal is then obtained by: 
$$
\hat{E}_s(\vec{r}) = OA\left[I\right] - OA\left[I^\text{ref}\right]
$$


Note that the high-frequency component could be quite small, and we are only interested in the static ones, those that do not fluctuate in time. It is thus a good idea to average the measure of the intensity of the reference over muliple mesurements to have a good estimation of the bias.

**TL;DR**

To remove the bias due to high spatial-frequency components of the reference, first apply the off-axis operation with the **reference only**, preferably by averaging. The resulting term is to be substracted from all estimation of the field when using the reference and the signal beam. 

### Global solution

In addition to the mesaurement of the intensity of the interference patter $I$, only one addition measurement is needed, the measure of the intensity of the reference $I^\text{ref}$. 

$$
\hat{E}_s(\vec{r}) 
    = \left(OA\left[I\right] - OA\left[I^\text{ref}\right]\right) / B(\vec{r})
$$

with 

$$
B(\vec{r}) = F^{-1} \circ Filter \circ F\left[I^\text{ref}(\vec{r})\right]
$$

- **Note 1.** the order of the operations, we need to first remove the biasbefore compensating for low the spatial frequency variations of the reference.
- **Note 2.** When measuring multiple complex signals, as long as the reference does not change, $I^\text{ref}$ needs to be measured only once.
- **Note 3.** The relative effect of the bias depends on the global intensity of the reference. The raw reconstructed signal is  \(E_\text{ref} E_s(\vec{r})\) is proportionnal to the square root of the reference intensity, while the bias is proportional to the intensity. When one has enough power to have a good signal to noise on the measurement of the interferogram, increasing the intensity of the reference may not be benificial.

## Effect of one measurement of the field (simulations)

![Simulated interferogram and Fourier transform](./images/simu_interferogram.png)

## Effect of the transmission matrix (experiment)

![Comparison of SVDs with and without bias compensation](./images/TM_svd_comp.png)