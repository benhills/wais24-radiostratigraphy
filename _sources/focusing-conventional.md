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
Readers who already have a background in SAR methods should skip forward to {doc}`multisquint` where we outline our algorithm for dipping englacial layers.
```

# Conventional radar focusing

<!--- SAR focusing -->

A synthetic aperture radar (SAR) moves as it transmits and receives radar pulses, forming a "synthetic" aperture by observing the same target from many locations as it passes.
The goal of SAR methods is to bring those many observations together to one point in an image, boosting the signal-to-noise ratio and improving the spatial resolution {cite}`Cumming2005`.
To sum the radar returns and coherently "focus" the radar image (i.e., with the waveforms having aligned phase), one must precisely know how the range to target is expected to change through the synthetic aperture. 
Here, we define a radar sounding geometry and use it to derive a range correction, first for the conventional case of a subaerial target (e.g., for spaceborne SAR applications); then, in {doc}`focusing-sounder` for radar sounding through two media (e.g., air and ice for an airborne survey of a glacier or ice sheet).
We then use the derived range function to calculate a matched filter to be correlated with the measured radar signal, much like the range-matched filter used for pulse compression but in the along-track dimension.

## SAR geometry

### Standard geometry

Considering radar-wave propagation through a single media, the propagation path from antenna to target is a direct ray. 
That is, for a given along-track location ($x$; commonly referred to as the azimuth for satellite platforms) the range is known through the Pythagorean theorem
```{math}
:label: SAR-range-standard
r = \sqrt{r_0^2 + (x-x_0)^2}
```
where $r_0$ and $x_0$ are the reference range and along-track distance, respectively, at the closest approach position of the instrument platform (i.e., directly over the target).
For a nadir-looking radar, $x_0 = 0$, and targets will manifest in the unfocused radar image as hyperbolae at their true along-track position. 

```{code-cell}
import numpy as np
r0 = 1000.     # range to target at closest approach
x0 = 0.        # along-track distance to target (nadir)
dx = .1        # x step
xs = np.arange(-100.,100+dx,dx) # along-track distances within the synthetic aperture
r = np.sqrt(r0**2+(xs-x0)**2)     # range to target as platform passes by
```

```{code-cell}
# Plot the range-offset function for a nadir-looking radar
import matplotlib.pyplot as plt
plt.figure(figsize=(4,3))
plt.axhline(0,color='grey',ls=':')
plt.axvline(0,color='grey',ls=':')
plt.plot(xs,r-r0,'k-')
plt.ylabel('Range offset (m)')
plt.xlabel('Along-track distance (m)')
plt.ylim(5,-1)
plt.xlim(min(xs),max(xs));
```

### Squinted geometry

For a "squinted" radar (angled forward or backward), $x_0 = r sin(\theta)$, so the hyperbola is skewed forward or backward depending on the squint angle, $\theta$.

```{code-cell}
import numpy as np
theta = -3*np.pi/180.  # squinted backward 3 degrees
x0 = r0*np.sin(theta)  # along-track distance to target (nadir)
r_sq = np.sqrt(r0**2+(xs-x0)**2)     # range to target for squinted geometry
```

```{code-cell}
# Plot the range-offset function for a nadir-looking radar
import matplotlib.pyplot as plt
plt.figure(figsize=(4,3))
plt.axhline(0,color='grey',ls=':')
plt.axvline(0,color='grey',ls=':')
plt.plot(xs,r_sq-r0,'k-')
plt.ylabel('Range offset (m)')
plt.xlabel('Along-track distance (m)')
plt.ylim(5,-1)
plt.xlim(min(xs),max(xs));
```

## SAR focusing

Using equation {eq}`SAR-range-standard`, we now define a phase offset to coherently align radar returns within a synthetic aperture,
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
plt.figure(figsize=(6,4))

# Calculate the matched filter for no squint
c = 3e8  # vacuum wave speed
fc = 150e6  # center frequency (Hz)
C = np.exp(-1j*4.*np.pi*fc*r/c)
# and for squinted
C_sq = np.exp(-1j*4.*np.pi*fc*r_sq/c)

ax1 = plt.subplot(211)
plt.plot(xs,np.real(C),'k-')
plt.subplot(212,sharex=ax1)
plt.plot(xs,np.real(C_sq),c='grey',ls='--')
plt.xlabel('Along-track distance (m)')
plt.ylabel('Reference waveform')
plt.xlim(min(xs),max(xs));
```

To focus the image, a matched filter {eq}`matched-filter` is calculated for every range bin, $r_0$, resolved by the radar receiver, and that filter is convolved with the radar measurements, $E$,
```{math}
:label: SAR-focusing
P_{jk} = \sum_{j=1}^N E_{jk}(r_{jk},x_j) e^{-i\phi_j}
```
where $j$ is the along-track index, $k$ the range index, and $N$ the number of along-track pixels included in the synthetic aperture.
One might define the extent of the SAR aperture relative to the beamwidth of the trasmitted wave which is related to the shape of the antenna, here simplified to its length.

```{code-cell}
# instrument properties
L_a = 15. # antenna length
theta_beam = 0.866 * 3e8/(fc*L_a) # "half-power beamwidth" of the antenna with respect to its Length
```

Now, the aperture extent changes with range as the radar energy spreads out. 
As an example here, we fill in an array with all the matched filters for every range bin. 

```{code-cell}
# preallocate array
theta_sq = 0.
x_start = r0*np.sin(theta_sq-theta_beam)
x_end = r0*np.sin(theta_sq+theta_beam)
Xs = np.arange(x_start,x_end+1)
C_ref = np.nan*np.zeros((int(r0),len(Xs)),dtype=np.complex128)

for i,ri in enumerate(range(int(r0))):
    # get aperture extents
    x = ri*np.sin(theta_beam)
    xs = np.arange(-x,x+.01)
    # now calculate the reference function placed in the oversized array
    r = np.sqrt(ri**2+xs**2)-ri
    C = np.exp(-1j*4.*np.pi*fc*r/c)
    C_ref[i,len(Xs)//2-int(x):len(Xs)//2-int(x)+len(xs)] = C
```

and do the same for the squinted geometry...

```{code-cell}
# preallocate array
theta_sq = -3*np.pi/180.
x_start = -r0*np.sin(theta_sq+theta_beam)
x_end = -r0*np.sin(theta_sq-theta_beam)
x_extent = max(abs(x_start),abs(x_end))
Xs_sq = np.arange(-x_extent-1,x_extent+1)
C_sq = np.nan*np.zeros((int(r0),len(Xs_sq)),dtype=np.complex128)

for i,ri in enumerate(range(int(r0))):
    # get aperture extents
    x_start = -ri*np.sin(theta_sq+theta_beam)
    x_end = -ri*np.sin(theta_sq-theta_beam)
    xs = np.arange(x_start,x_end+1)
    # now calculate the reference function placed in the oversized array
    r = np.sqrt(ri**2+xs**2)-ri
    C = np.exp(-1j*4.*np.pi*fc*r/c)
    #print(x_start,x_end,np.shape(C),np.shape(xs))
    C_sq[i,len(Xs_sq)//2+int(x_start):len(Xs_sq)//2+int(x_start)+len(xs)] = C
```

```{code-cell}
plt.figure(figsize=(8,3))

plt.subplot(121)
plt.pcolormesh(Xs,np.arange(r0),np.real(C_ref),cmap='twilight_shifted')
plt.ylim(r0,0)
plt.xlim(-150,150)
plt.xlabel('Along-track Distance (m)')
plt.ylabel('Range (m)');

plt.subplot(122)
plt.pcolormesh(Xs_sq,np.arange(r0),np.real(C_sq),cmap='twilight_shifted')
plt.ylim(r0,0)
plt.xlim(-150,150)
plt.colorbar(label='Reference waveform')
plt.tight_layout()
```