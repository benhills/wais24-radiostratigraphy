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

Individual radar reflections are rarely used as a final data product.
Instead, synthetic aperture radar (SAR) focusing is used to improve the spatial resolution by coherently stacking traces within a prescribed ''aperture'' (number of neighboring traces).
The most common SAR focusing algorithms (e.g., the ''standard'' product from the Center for Remote Sensing and Integrated Systems, CReSIS) are optimized for diffuse reflectors, assuming that the same feature can create an observable echo from multiple directions.
Then, data are coherently stacked along a hyperbola to ''focus'' the data (commonly done in the frequency domain).
Focused products work well for the bed echo which is rough at the radar wavelength and therefore diffuse.
However, the specular englacial echoes are not always well resolved in these products.
In fact, the hyperbolic stacking can create destructive interference in dipping layers---in some cases causing them to vanish entirely.
As an alternative, {cite}`castelletti2019layer` proposed a ''layer-optimized''' SAR (LOSAR) algorithm which stacks along a linear dip angle within the synthetic aperture.
The authors demonstrated the algorithm with a single dataset from the British Antarctic Survey (BAS), but it is applicable to any phase-coherent profiling radar sounder.

LOSAR images have better-resolved radiostratigraphy compared to prior data products.
The improved result can be used for better interpretations of individual layers of interest and to generate more precise glaciological {cite}`sanderson2023_EnglacialAntarctica`{cite}`Catania2006SequentialAntarctica`{cite}`Conway2002SwitchStream` and paleoclimatological {cite}`lilien2021_BriefDomeC`{cite}`Cavitte2021AYears`{cite}`Bodart2021Age-DepthChronology` insight.
Importantly, there is also a secondary output from the LOSAR algorithm: the dip angle of the layer itself, chosen as the Doppler frequency/wavenumber with the greatest power.
Since the layer dip can be estimated algorithmically, the shapes of layers can be considered without any need for manual interpretation of individual reflection horizons (as has been done in past work; e.g., {cite}`Macgregor2015Radiostratigraphy`).
Similar work has done the same layer-dip estimation, even for radars which are not phase coherent, through image processing techniques {cite}`Holschuh2017DecodingSlopes`{cite}`Panton2014AutomatedEchograms`{cite}`Delf2020ASheets`, but with less capability to recover the stratigraphy.
High fidelity, algorithmically acquired layer dips across full survey campaigns can be used to guide phase-based layer tracking {cite}`Macgregor2015Radiostratigraphy`, or as we propose here to interpret the ice dynamics and ice-sheet history directly.

In order to 
In {doc}`multisquint` we explore an alternative.

```{note}
Here is a note
```

## Standard Focusing

Text here

$$
e=mc^2
$$

```{code-cell}
import numpy as np
Lambda = 1.                 # radar wavelength
r = 100.                    # depth of feature
a = np.arange(-100,101)     # along-track distance 
ref_r = np.sqrt(r**2+a**2)  # distance to target as platform passes by
ref_phi = np.exp(-1j*4.*np.pi*ref_r/Lambda) # reference phase history
```

```{code-cell}
import matplotlib.pyplot as plt
plt.figure()
plt.subplot(211)
plt.plot(a,ref_r)
plt.ylabel('range')
plt.subplot(212)
plt.plot(a,np.angle(ref_phi));
plt.ylabel('reference phase')
```

## ''Layer Optimized'' Focusing


```{code-cell}
Lambda = 1.                 # radar wavelength
theta = 10.*np.pi/180.      # dip angle
a = np.arange(-100,101)     # along-track distance 
ref_r = a*np.arctan(theta)  # distance to target as platform passes by
ref_phi = np.exp(-1j*4.*np.pi*ref_r/Lambda) # reference phase history
```

```{code-cell}
plt.figure()
plt.subplot(211)
plt.plot(a,ref_r)
plt.ylabel('range')
plt.subplot(212)
plt.plot(a,np.angle(ref_phi));
plt.ylabel('reference phase')
```


