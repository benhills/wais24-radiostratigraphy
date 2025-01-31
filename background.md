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

<!--- SAR focusing -->
As a radar instrument moves through a synthetic aperture, the range from instrument and target changes between acquisitions.
Hence, waveform stacking within a SAR algorithm requires a range (or phase) correction if it is to coherently "focus" the data, improving both the signal-to-noise ratio and the spatial resolution {cite}`Cumming2005`.
Below, we define a radar sounding geometry and use it to derive a range correction, first for the conventional case of a subaerial target (e.g., for spaceborne SAR applications), then for RES through two media (e.g., air and ice for an airborne survey of a glacier or ice sheet).
We then use the derived range function to calculate a matched filter to be correlated with the measured radar signal, much like the range-matched filter used for pulse compression but in the along-track dimension.

```{note}
This section gives a high-level background on sar focusing and compression in the along-track direction (sometimes called azimuth compression in conventional SAR textbooks {cite}`Cumming2005`). Readers with a background in SAR methods should skip forward to {doc}`multisquint` where we define and apply our alternative algorithm suited for dipping englacial layers.
```

### Radar sounding geometry

Considering radar-wave propagation through a single media (i.e., for subaerial SAR applications), the propagation path from the radar antenna to target is a direct ray. That is, for a given along-track location ($x$; commonly referred to as the azimuth for satellite platforms) the range is known through the Pythagorean theorem
```{math}
:label: SAR-range-standard
r = \sqrt{r_0^2+(x-x_0)^2}
```
where $r_0$ and $x_0$ are the reference range and along-track distance, respectively.
Here, we will take those reference positions at the closest approach position of the instrument platform (i.e. directly over the target).

```{code-cell}
import numpy as np
r0 = 1000.     # range to target at closest approach (assume x0 is 0)
dx = .1        # x step
Xs = np.arange(-100.,100+dx,dx) # along-track distances within the synthetic aperture
r = np.sqrt(r0**2+Xs**2)     # range to target as platform passes by
```

```{code-cell}
# Plot the range-offset function
import matplotlib.pyplot as plt
plt.figure(figsize=(4,3))
plt.axhline(0,color='grey',ls=':')
plt.axvline(0,color='grey',ls=':')
plt.plot(Xs,r-r0,'k-')
plt.ylabel('Range offset (m)')
plt.xlabel('Along-track distance (m)')
plt.ylim(5,-1);
```

In RES through two media, equation {eq}`SAR-range-standard` is complicated slightly by refraction at the air-ice interface.
Reframing in terms of the vertical coordinate, it can be shown by the survey geometry ({numref}`SAR-geometry`) that
```{math}
:label: SAR-range-raybend
r = r_{air} + r_{ice} = \frac{h}{\cos\theta} + \frac{d}{\cos\theta_{ice}}
```
where $h$, the height of the instrument platform above the ice surface, and $d$, the depth of the target below the ice surface, are known and assumed to be fixed within a given synthetic aperture.
If the $h$ is not fixed due to motion of the platform, it is assumed that motion can be compensated.

```{figure} ./figures/SAR_geometry.png
---
height: 300px
name: SAR-geometry
---
Illustration of SAR geometry for wave propagation through two media and refraction at their interface.
```

There are two additional unknown variables which must be related to $x$ in order to build an expected range function for the synthetic aperture, $r(x)$, similar to equation {eq}`SAR-range-standard`.
Those are the squint angle, $\theta$, and its associated angle after ray bending into the ice, $\theta_{ice}$.
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
now with the only $n$ being that for the second medium (ice; $n\approx1.77$).
Second, the propagation angle can be related to the range from instrument to target by assuming that the squint angle is relatively small ($\tan\theta=n\tan\theta_{ice}$)
```{math}
:label: angle-approximation
\tan\theta = \frac{x-x_0}{h+d/n}
```
Then, substituting equations {eq}`snells-law-2` and {eq}`angle-approximation` into equation {eq}`SAR-range-raybend` gives the relative range as a function of position within the synthetic aperture,

```{math}
:label: SAR-range-raybend-full
r = \frac{h}{\cos \left ( \tan^{-1} \left ( \frac{x-x_0}{h+d/n} \right ) \right )} 
+   \frac{d}{\cos \left ( \tan^{-1} \left ( \frac{x-x_0}{nh+d} \right ) \right ) }
```

```{note}
The code cells below use functions from external python scripts. See the github repository ([here](https://github.com/benhills/wais24-radiostratigraphy)) to access those scripts.
```

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
# Plot the range-offset function
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

### SAR focusing

Using the range equations given in the previous section (i.e. equations {eq}`SAR-range-standard` or {eq}`SAR-range-raybend`), we define a phase offset to coherently align waveforms within a synthetic aperture,
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
which is used as a matched filter against the measured radar signal.

```{code-cell}
# Import functions from external scripts (see the note above)
from sar_functions import *
from supplemental import *

# Calculate and plot the matched filter for no squint
plt.figure(figsize=(6,2.5))
C = matched_filter(r2p(R1_air))
plt.plot(Xs,np.real(C),'k--')
C = matched_filter(r2p(R1_ice))
plt.plot(Xs,np.real(C),'k-')
plt.xlabel('Along-track distance (m)')
plt.ylabel('Reference waveform');
```

```{code-cell}
# Calculate and plot the matched filter with squint
plt.figure(figsize=(6,2.5))
C = matched_filter(r2p(R2_air))
plt.plot(Xs,np.real(C),'--',c='indianred')
C = matched_filter(r2p(R2_ice))
plt.plot(Xs,np.real(C),'-',c='indianred')
plt.xlabel('Along-track distance (m)')
plt.ylabel('Refeference waveform');
```

To focus the image, a matched filter {eq}`matched-filter` is calculated for every range bin, $r_0$, and convolved with the radar measurements, $E$,
```{math}
:label: SAR-focusing
P_{jk} = \sum_{j=1}^N E_{jk}(r_{jk},x_j) e^{-i\phi_j}
```
where $j$ is the along-track index, $k$ the range index, and $N$ the number of along-track pixels included in the synthetic aperture.