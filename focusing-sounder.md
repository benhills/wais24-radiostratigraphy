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

```{note}
This section gives a high-level background on sar focusing and compression in the along-track direction (sometimes called azimuth compression in conventional SAR textbooks {cite}`Cumming2005`). Readers with a background in SAR methods should skip forward to {doc}`multisquint` where we define and apply our alternative algorithm suited for dipping englacial layers.
```

# Radar sounder focusing 

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
import numpy as np
import matplotlib.pyplot as plt
from sar_geometry import *

# define the geometry
c = 3e8
t0 = 4e-6     # range to target at closest approach (assume x0 is 0)
h = 500 # height above ice surface
dx = .1        # x step
Xs = np.arange(-100.,100+dx,dx) # along-track distances within the synthetic aperture

# for a given squint angle (theta) find the depth in ice 
# and along-track distance (x0) from center of aperture to target
theta_sq = 1e-10*np.pi/180.
r0 = t0*c
# range offset within aperture - air only so simple geometry
R1_air = (np.sqrt(r0**2.+Xs**2.) - r0)/c
# range offset within aperture - with ice so ray bending
R1_ice = SAR_aperture_raybend(t0, h, Xs, theta_sq)

# again with non-zero squint
theta_sq = -3.*np.pi/180.
# range offset within aperture - air only so simple geometry
x0 = r0*np.sin(theta_sq)   # distance from center of aperture to closest approach
h_air = r0*np.cos(theta_sq)    # vertical height above target at closest approach
R2_air = (np.sqrt(h_air**2.+(Xs-x0)**2.) - h_air)/c
# range offset within aperture - with ice so ray bending
d, x0 = get_depth_dist(t0,h,theta_sq)
R2_ice = SAR_aperture_raybend(t0, h, Xs, theta_sq)
```

```{code-cell}
# Plot the range-offset function, now in time
plt.figure(figsize=(4,3))
plt.axhline(0,color='grey',ls=':')
plt.axvline(0,color='grey',ls=':')
plt.plot(Xs,R1_air*1e6,'k',ls='--',label='Nadir Air Only')
plt.plot(Xs,R1_ice*1e6,'k',label='Nadir Air/Ice')
plt.plot(Xs,R2_air*1e6,'indianred',ls='--',label='Squinted Air Only')
plt.plot(Xs,R2_ice*1e6,'indianred',label='Squinted Air/Ice')
plt.legend(fontsize=8)
plt.xlabel('Along-track distance (m)')
plt.ylabel('Range offset ($\mu$sec)')
plt.ylim(.04,-.01)
plt.xlim(min(Xs),max(Xs));
```

```{code-cell}
# Calculate and plot the matched filter for no squint
plt.figure(figsize=(6,2.5))
C = matched_filter(r2p(R1_air))
plt.plot(Xs,np.real(C),'k--')
C = matched_filter(r2p(R1_ice))
plt.plot(Xs,np.real(C),'k-')
plt.xlabel('Along-track distance (m)')
plt.ylabel('Reference waveform')
plt.xlim(min(Xs),max(Xs));
```

```{code-cell}
# Calculate and plot the matched filter with squint
plt.figure(figsize=(6,2.5))
C = matched_filter(r2p(R2_air))
plt.plot(Xs,np.real(C),'--',c='indianred')
C = matched_filter(r2p(R2_ice))
plt.plot(Xs,np.real(C),'-',c='indianred')
plt.xlabel('Along-track distance (m)')
plt.ylabel('Refeference waveform')
plt.xlim(min(Xs),max(Xs));
```

```{code-cell}
### instrument properties
fc = 150e6 # center frequency
L_a = 7.5 # antenna length
theta_beam = 0.866 * 3e8/(fc*L_a) # "half-power beamwidth" of the antenna with respect to its Length

c = 3e8
t0 = 4e-6
h = 500
dt = 2e-8
dx = 0.1
theta_sq = -3.*np.pi/180.

Xs, ind_start_max, ind_end_max = aperture_extent(t0, h, theta_sq, theta_beam, dx=dx)
if ind_start_max > 0:
    ind_start_max = 0
    Xs = np.arange(0,max(Xs),dx)
n_x_max = ind_end_max-ind_start_max

C_ref_all = np.zeros((int(t0/dt),n_x_max+1),dtype=np.complex128)
for si,ti in enumerate(np.arange(0,t0,dt)):
    # get aperture extents
    x, ind_start, ind_end = aperture_extent(ti, min(h,ti*c), theta_sq, theta_beam, dx=dx)

    # now calculate the reference function placed consistently in the oversized array
    r = SAR_aperture_raybend(ti, min(h,ti*c), x, theta_sq)
    C_ref = matched_filter(r2p(r))

    C_ref_all[si,ind_start-ind_start_max:ind_end-ind_start_max+1] = C_ref

plt.figure()
plt.pcolormesh(Xs,np.arange(0,t0,dt)*1e6,np.real(C_ref_all),cmap='twilight_shifted')
plt.colorbar(label='Reference waveform')
plt.ylim(t0*1e6,0)
plt.axhline(h/c*1e6,color='k',linestyle='--')
plt.xlabel('Along-track Distance (m)')
plt.ylabel('Range ($\mu$sec)');
```