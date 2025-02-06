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

# Focusing with a squint

<!--- What is a squint? -->

Squinted geometry
Squint angle moves the {eq}`SAR-range-standard`


```{code-cell}
import numpy as np
r0 = 1000.     # range to target at closest approach (assume x0 is 0)
dx = .1        # x step
Xs = np.arange(-100.,100+dx,dx) # along-track distances within the synthetic aperture
theta_sq = 3.*np.pi/180.   # squint angle of 3 degrees
x0 = r0*np.sin(theta_sq)   # distance from center of aperture to closest approach
h = r0*np.cos(theta_sq)    # vertical height above target at closest approach
r = np.sqrt(h**2+(Xs-x0)**2)     # range to target as platform passes by
```

```{code-cell}
# Plot the range-offset function
import matplotlib.pyplot as plt
plt.figure(figsize=(4,3))
plt.axhline(0,color='grey',ls=':')
plt.axvline(0,color='grey',ls=':')
plt.plot(Xs,r-h,'k-')
plt.ylabel('Range offset (m)')
plt.xlabel('Along-track distance (m)')
plt.ylim(5,-1);
```

Same matched filter as before

```{code-cell}
# Import functions from external scripts (see the note above)
from sar_functions import *
from supplemental import *

# Calculate and plot the matched filter for no squint
plt.figure(figsize=(6,2.5))
C = matched_filter(r2p(r-h))
plt.plot(Xs,np.real(C),'k-')
#C = matched_filter(r2p(r_squinted))
#plt.plot(Xs,np.real(C),'--',c='indianred')
plt.xlabel('Along-track distance (m)')
plt.ylabel('Reference waveform');
```