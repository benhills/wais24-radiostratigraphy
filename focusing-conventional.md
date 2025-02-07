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

# Conventional radar focusing

<!--- SAR focusing -->
As a radar instrument moves through a synthetic aperture, the range from instrument and target changes between acquisitions.
Hence, waveform stacking within a SAR algorithm requires a range (or phase) correction if it is to coherently "focus" the data, improving both the signal-to-noise ratio and the spatial resolution {cite}`Cumming2005`.
Below, we define a radar sounding geometry and use it to derive a range correction, first for the conventional case of a subaerial target (e.g., for spaceborne SAR applications), then for RES through two media (e.g., air and ice for an airborne survey of a glacier or ice sheet).
We then use the derived range function to calculate a matched filter to be correlated with the measured radar signal, much like the range-matched filter used for pulse compression but in the along-track dimension.

### SAR geometry

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

Squinted geometry

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
plt.figure(figsize=(6,2.5))

# Calculate the matched filter for no squint
c = 3e8  # vacuum wave speed
fc = 150e6  # center frequency (Hz)
C = np.exp(-1j*4.*np.pi*fc*r/c)

plt.plot(Xs,np.real(C),'k-')
plt.xlabel('Along-track distance (m)')
plt.ylabel('Reference waveform');
```

To focus the image, a matched filter {eq}`matched-filter` is calculated for every range bin, $r_0$, and convolved with the radar measurements, $E$,
```{math}
:label: SAR-focusing
P_{jk} = \sum_{j=1}^N E_{jk}(r_{jk},x_j) e^{-i\phi_j}
```
where $j$ is the along-track index, $k$ the range index, and $N$ the number of along-track pixels included in the synthetic aperture.

```{code-cell}
# instrument properties
fc = 150e6 # center frequency
L_a = 15. # antenna length
theta_beam = 0.866 * 3e8/(fc*L_a) # "half-power beamwidth" of the antenna with respect to its Length
```

```{code-cell}
# preallocate array
x0 = r0*np.sin(theta_beam)
Xs = np.arange(-x0,x0+1)
C_ref_all = np.nan*np.zeros((int(r0),len(Xs)),dtype=np.complex128)

for i,ri in enumerate(range(int(r0))):
    # get aperture extents
    x = ri*np.sin(theta_beam)
    xs = np.arange(-x,x+.01)
    # now calculate the reference function placed in the oversized array
    r = np.sqrt(ri**2+xs**2)-ri
    C = np.exp(-1j*4.*np.pi*fc*r/c)
    C_ref_all[i,len(Xs)//2-int(x):len(Xs)//2-int(x)+len(xs)] = C
```

```{code-cell}
plt.figure(figsize=(4,3))
plt.pcolormesh(Xs,np.arange(r0),np.real(C_ref_all),cmap='twilight_shifted')
plt.ylim(r0,0)
plt.colorbar(label='Reference waveform')
plt.xlabel('Along-track Distance (m)')
plt.ylabel('Range (m)');
```