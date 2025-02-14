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

# Multi-squinted sounder focusing

The most commonly used synthetic aperture radar (SAR) processing algorithms {cite}`OPR2024` are optimized for targets which are rough at the scale of the radar wavelength, scattering energy in all directions (diffuse), and are therefore observable from the many aspects across the synthetic aperture.
However, englacial targets are commonly smooth relative to the radar wavelength (specular) and some targets such as dipping layers are poorly resolved {cite}`Karlsson2009TheData` {cite}`Winter2015AirborneDynamics`
The radar antennas used for sounding through ice have a small *real* aperture, meaning that the real beamwidth is large and some portion of the radar wave is directed off nadir {cite}`Heister2018CoherentData`.
It is therefore expected that some amount of radar power should be received even from the dipping specular layers, but that destructive interference within the synthetic aperture can diminish power for those layers {cite}`holschuh2014power`.
As an alternative, Castelletti et al. {cite}`castelletti2019layer` proposed a “layer-optimized“ SAR (LOSAR) algorithm which stacks along a linear dip angle within the synthetic aperture (assuming perfect specularity). 
LOSAR images have better-resolved radiostratigraphy compared to prior data products; however, the LOSAR algorithm assumes that features are linear within the synthetic aperture which can lead to unrealistic abrupt angles in the processed image.

Here, we outline another SAR processig method which uses synthetic squint angles {cite}`Ferro2019SquintedEnhancement`{cite}`Rodriguez2009MultiSquintRS`.
That is, even though the radar antenna is directed at nadir, the beam width is large enough that small sub apertures can be considered within that beam that themselves are squinted forward or backward.
As seen in {numref}`multi-squint-movie`, dipping englacial layers can be resolved more clearly when using a synthetic squint. 
To maximize the signal-to-noise ratio for every englacial target, a local focusing of the image near that target is required.
Below, we outline how the local squint angle may be chosen based on the measured Doppler spectra and how each of the locally focused pixels could be compiled together into a single mosaic image.

```{figure} ./figures/multisquint-movie.gif
---
height: 400px
name: multi-squint-movie
---
Image processed across a range of squint angles following the methods given in {doc}`focusing-sounder`.
```

## Ideal squint angle from Doppler spectra

The optimal squint anglel for a given pixel in the image could be selected in multiple ways.
For example, Castelletti et al. {cite}`castelletti2019layer` iterate through all plausible englacial layer dips and choose that angle which maximizes the coherently stacked power.
Another approach, which we take here, is to use the frequency information in the along-track direction.
Based on the Doppler effect, frequency content should increase if the instrument platform is moving toward the target (up-dipping layers or forward squinted) and should decrease if moving away.
Take some sample from the pulse-compressed image ({numref}`doppler-spectra`), the Doppler frequencies are calculated by Fourier transform in the along-track dimension for a given range bin.

```{figure} ./figures/doppler-spectra.png
---
height: 400px
name: doppler-spectra
---
Pulse compressed radar sounding image (left) and associated Doppler spectra (right) for each range bin at the selected along-track location (vertical line at ~3 km).
```

To calculate the optimal squint angle from Doppler frequencies, we first choose that frequency with the most power, the "Doppler centroid", $f_\mathrm{dc}$, which is proportional to the expected phase gradient 

```{math}
:label: Fdc-phase-gradient
f_{\mathrm{dc}} = \frac{1}{2\pi}\frac{\partial \phi}{\partial \tau}
```
where $\tau$ is the "slow" time between traces. That time is related to along-track distance through the platform velocity, and phase is related to range (as in {doc}`focusing-conventional`),
```{math}
:label: Fdc-range-gradient
f_{\mathrm{dc}} = \frac{2 v f_\mathrm{c}}{c}\frac{\partial r}{\partial x}
```
Finally, the change in range with along-track position is approximately $n\sin(\theta)$ (although an exact solution is possible as in equation {eq}`quartic`, so
```{math}
:label: Fdc-squint
f_{\mathrm{dc}} = \frac{2 v f_\mathrm{c}}{c} n\sin(\theta)
```
and solving for the squint angle
```{math}
:label: squint-Fdc
\theta = \sin^{-1} \left ( \frac{c f_{\mathrm{dc}}}{2 n v f_\mathrm{c}} \right )
```

## Compiling a multi-squinted mosaic

To maximize the signal-to-noise ratio for all targets in a single image, a mosaic across the many squint angles must be created.
Following {numref}`multi-squint-flowchart`, we propose an algorithm which does local focusing for every pixel in the image.
After the standard pre-processing and range compression, the algorithm iterates through all traces and all range bins for every trace, taking these steps for each:
1. find the local squint angle from measured Doppler spectra (as above).
2. define the aperture extent based on the squint angle, range bin, and synthetic beam width (smaller than the real beam).
3. range migration (not discussed here but an important step especially for large squint angles).
4. along-track compression using the matched filter as described in {doc}`focusing-sounder`.

```{figure} ./figures/squint-flowchart.png
---
height: 200px
name: multi-squint-flowchart
---
Plausible algorithm design for multi-squinted synthetic aperture radar focusing.
```