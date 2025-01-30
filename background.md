---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Background

<!--- SAR processing -->
Individual radar reflections are rarely used as a final data product.
Instead, synthetic aperture radar (SAR) focusing is used to improve the spatial resolution by coherently stacking traces within a prescribed ''aperture'' (number of neighboring traces).
The most common SAR focusing algorithms (e.g., the ''standard'' product from the Center for Remote Sensing and Integrated Systems, CReSIS) are optimized for diffuse reflectors, assuming that the same feature can create an observable echo from multiple directions.
Then, data are coherently stacked along a hyperbola to ''focus'' the data (commonly done in the frequency domain).

```{note}
In {doc}`multisquint` we explore an alternative.
Below, we give added background for context.
```

## Radar Sounding Geometry

For a given along-track location ($x_j$; commonly referred to as the azimuth for satellite platforms) the range is
```{math}
:label: SAR-range-standard
r_j = \sqrt{r_0^2+(x_j-x_0)^2}
```
where $r_0$ and $x_0$ are the reference range and along-track distance, respectively.
Here, we will take those reference positions at the closest approach position of the instrument (i.e. directly over the target).

```{code-cell}
import numpy as np
r0 = 1000.     # depth of feature (assume x0 is 0)
dx = .1        # x step
Xs = np.arange(-100.,100+dx,dx) # along-track distances within the aperture
r = np.sqrt(r0**2+Xs**2)     # distance to target as platform passes by
```

```{code-cell}
import matplotlib.pyplot as plt
plt.figure()
plt.axhline(0,color='grey',ls=':')
plt.axvline(0,color='grey',ls=':')
plt.plot(Xs,r-r0,'k-')
plt.ylabel('Range offset (m)')
plt.xlabel('Along-track distance (m)')
plt.ylim(5,-1);
```

In RES through two media, equation {eq}`SAR-range-standard` is complicated slightly by ray bending at the air-ice interface.
Reframing in terms of the vertical coordinate, it can be shown by the survey geometry that
```{math}
:label: SAR-range-raybend
r_j = r_{j,air} + r_{j,ice} = \frac{h}{\cos\theta_j} + \frac{d}{\cos\theta_{j,ice}}
```
where $h$, the height of the instrument above the ice surface, and $d$, the depth of the target below the ice surface, are known and assumed to be fixed within a given synthetic aperture. 

```{figure} ./figures/SAR_geometry.png
---
height: 300px
name: SAR_geometry
---
SAR geometry
```

The two additional unknown variables must be related to $x_j$ in order to build an expected range function for the synthetic aperture, $r(x)$, similar to equation \ref{eq:SAR-range-standard}.
Those are the squint angle, $\theta_j$, and its associated angle after ray bending into the ice, $\theta_{j,ice}$.
First, the two propagation angles are related through Snell's law,
```{math}
:label: snells-law
\frac{\sin\theta_1}{\sin\theta_2} = \frac{n_2}{n_1}
```
where $n=\sqrt{\epsilon}$ is the refractive index and $\epsilon$ the permittivity.
When the first material is air, $n_1\approx1$, and equation {eq}`snells-law` reduces to
```{math}
:label: snells-law-2
\sin\theta = n\sin\theta_{ice}
```
now with the only $n$ being that for the second medium (ice; 1.77).
Second, the propagation angle can be related to the distance to the target by assuming a small angle
```{math}
:label: angle-approximation
\tan\theta = \frac{x-x_0}{h+d/n}
```
Then, using equations {eq}`snells-law-2` and {eq}`angle-approximation` in equation {eq}`SAR-range-raybend` gives the relative range as a function of position within the synthetic aperture.

```{code-cell}
from sar_geometry import *
h = 200 # height above ice surface

# for a given squint angle (theta) find the depth in ice 
# and along-track distance (x0) from center of aperture to target
theta = 1e-10*np.pi/180. # divide by zero gives an error so give a small number
d, x0 = get_depth(r0,h,theta)
# range offset within aperture - air only so simple geometry
R1_air = np.sqrt(r0**2.+(Xs-x0)**2.) - r0
# range offset within aperture - with ice so ray bending
R1_ice = SAR_aperture_raybend(r0, h, Xs, theta)

# again with non-zero squint
theta = -3.*np.pi/180.
d, x0 = get_depth(r0,h,theta)
# range offset within aperture - air only so simple geometry
R2_air = np.sqrt((h+d)**2.+(Xs-x0)**2.) - h - d
# range offset within aperture - with ice so ray bending
R2_ice = SAR_aperture_raybend(r0, h, Xs, theta)
```

```{code-cell}
# Initialize the figure
plt.figure(figsize=(4,3))
plt.axhline(0,color='grey',ls=':')
plt.axvline(0,color='grey',ls=':')

plt.plot(Xs,R1_air,'k',ls='--',label='Nadir Air Only')
plt.plot(Xs,R1_ice,'k',label='Nadir Air/Ice')
plt.plot(Xs,R2_air,'indianred',ls='--',label='Squinted Air Only')
plt.plot(Xs,R2_ice,'indianred',label='Squinted Air/Ice')

plt.legend(fontsize=8)
plt.xlabel('Along-track distance (m)')
plt.ylabel('Range offset (m)')
plt.ylim(5,-1);
```

## SAR Focusing

Using the range equation given in the previous section (i.e. equation \ref{eq:SAR-range-raybend} in our case of two media), we define a phase offset to coherently align waveforms within a synthetic aperture,
```{math}
:label: range-to-phase
\phi = \frac{4 \pi f_\mathrm{c}}{c} r
```
where $f_\mathrm{c}$ is the instrument center frequency, and $c$ is the vacuum wave speed.
This reference phase is converted to a complex number
```{math}
:label: matched-filter
C_j = e^{-i\phi}
```
which is used as a matched filter against the measurements.

```{code-cell}
ref_phi = np.exp(-1j*4.*np.pi*ref_r/Lambda) # reference phase history

plt.figure()
plt.subplot(211)
plt.plot(a,ref_r)
plt.ylabel('range')
plt.subplot(212)
plt.plot(a,np.angle(ref_phi))
plt.ylabel('reference phase');
```

Integer justo tortor, auctor id mi et, hendrerit mollis est. Cras laoreet diam augue, eu semper ipsum luctus in. Phasellus lacinia enim libero, sed gravida tortor ultricies ut. Cras consequat at tortor et egestas. Etiam scelerisque et lacus et eleifend. Aenean mattis ligula at bibendum pellentesque. Pellentesque facilisis velit velit, et elementum quam faucibus sed. Praesent sodales lorem ac pellentesque dapibus. Morbi rutrum est vel interdum tincidunt. Proin ac nulla libero. Proin metus arcu, commodo tempor vestibulum vel, posuere eget diam. Sed scelerisque lorem sed libero tristique, eget aliquet augue fermentum. Aenean malesuada justo lectus, ut ornare nisi viverra at. Phasellus eget lectus sem. Duis faucibus diam justo, mollis rhoncus eros tincidunt nec. Sed ultricies nisl urna, a lacinia velit commodo eget.

```{math}
:label: SAR-focusing
P_k = \sum E_j(r_j,S_j) exp[-i\phi]
```

Integer justo tortor, auctor id mi et, hendrerit mollis est. Cras laoreet diam augue, eu semper ipsum luctus in. Phasellus lacinia enim libero, sed gravida tortor ultricies ut. Cras consequat at tortor et egestas. Etiam scelerisque et lacus et eleifend. Aenean mattis ligula at bibendum pellentesque. Pellentesque facilisis velit velit, et elementum quam faucibus sed. Praesent sodales lorem ac pellentesque dapibus. Morbi rutrum est vel interdum tincidunt. Proin ac nulla libero. Proin metus arcu, commodo tempor vestibulum vel, posuere eget diam. Sed scelerisque lorem sed libero tristique, eget aliquet augue fermentum. Aenean malesuada justo lectus, ut ornare nisi viverra at. Phasellus eget lectus sem. Duis faucibus diam justo, mollis rhoncus eros tincidunt nec. Sed ultricies nisl urna, a lacinia velit commodo eget.