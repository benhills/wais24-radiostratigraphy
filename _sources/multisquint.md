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

# Multi-Squint Focusing

Text here

$$
e=mc^2
$$

```{code-cell}
import numpy as np
import matplotlib.pyplot as plt

plt.figure()
ax1 = plt.subplot(211)
plt.ylabel('range')
ax2 = plt.subplot(212)
plt.ylabel('reference phase');

r = 100
theta_L_a = 0.5
Lambda = 1.

for theta in np.arange(0,0.5,0.1):
    rho_ca = r * np.cos(theta) # 'closest approach' range position for squinted geometry
    a0 = rho_ca * np.tan(theta-theta_L_a/2.) # closest approach azimuth position for squinted geometry
    a_end = rho_ca * np.tan(theta+theta_L_a/2.) # half beamwidth, w/ squint, relative to s_im
    azs = np.linspace(a0,a_end,200)
    ref_r = np.sqrt((r*np.cos(theta))**2+azs**2)  # distance to target as platform passes by
    ref_phi = np.exp(-1j*4.*np.pi*ref_r/Lambda) # reference phase history

    ax1.plot(azs,ref_r)
    ax2.plot(azs,np.angle(ref_phi))
```

```{code-cell}
from ipywidgets import interact

x = np.linspace(0, 2 * np.pi)
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
line, = ax.plot(x, np.sin(x))

def update(w = 1.0):
    line.set_ydata(np.sin(w * x))
    fig.canvas.draw()

interact(update)
```
