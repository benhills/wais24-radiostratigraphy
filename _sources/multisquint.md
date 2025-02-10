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

The most commonly used synthetic aperture radar (SAR) processing algorithms {cite}`CRESIS` are optimized for targets which are rough at the scale of the radar wavelength, scattering energy in all directions (diffuse), and are therefore observable from the many aspects across the synthetic aperture.
However, englacial targets are commonly smooth relative to the radar wavelength (specular) and some targets such as dipping layers are poorly resolved {cite}`Karlsson2009TheData` {cite}`Winter2015AirborneDynamics`
The radar antennas used for sounding through ice have a small *real* aperture, meaning that the real beamwidth is large and some portion of the radar wave is directed off nadir {cite}`Heister2018CoherentData`.
It is therefore expected that some amount of radar power should be received even from the dipping specular layers, but that destructive interference within the synthetic aperture can diminish power for those layers {cite}`holschuh2014power`.
As an alternative, Castelletti et al. {cite}`castelletti2019layer` proposed a “layer-optimized“ SAR (LOSAR) algorithm which stacks along a linear dip angle within the synthetic aperture (assuming perfect specularity). 
LOSAR images have better-resolved radiostratigraphy compared to prior data products; however, the LOSAR algorithm assumes that features are linear within the synthetic aperture which can lead to unrealistic abrupt angles in the processed image.

Here, we outline another SAR processig method which uses synthetic squint angles {cite}`Ferro2019SquintedEnhancement`{cite}`Rodriguez2009MultiSquintRS`.
That is, even though the radar antenna is directed at nadir, the beam width is large enough that small sub apertures can be considered within that beam that themselves are squinted forward or backward.
As seen in {numref}`multi-squint-movie`, dipping englacial layers can be resolved more clearly when using a synthetic squint. 
To maximize the signal-to-noise ratio for every englacial target, a local focusing of the image near that target is required.
{cite}`Karlsson2012ALayers`
{cite}`Conway2002SwitchStream`
{cite}`Siegert2013LateAntarctica`
Below, we outline how the local squint angle may be chosen based on the Doppler centroid, and finally all squinted focusing compiled together into a single mosaic image.

```{figure} ./figures/multisquint-movie.gif
---
height: 400px
name: multi-squint-movie
---
Image processed across a range of squint angles following the methods given in {doc}`background`.
```

## Ideal squint angle from Doppler spectra

Integer justo tortor, auctor id mi et, hendrerit mollis est. Cras laoreet diam augue, eu semper ipsum luctus98
in. Phasellus lacinia enim libero, sed gravida tortor ultricies ut. Cras consequat at tortor et egestas. Etiam99
scelerisque et lacus et eleifend. Aenean mattis ligula at bibendum pellentesque. Pellentesque facilisis velit100
velit, et elementum quam faucibus sed. Praesent sodales lorem ac pellentesque dapibus. Morbi rutrum est101
vel interdum tincidunt. Proin ac nulla libero. Proin metus arcu, commodo tempor vestibulum vel, posuere102
eget diam. Sed scelerisque lorem sed libero tristique, eget aliquet augue fermentum. Aenean malesuada103
justo lectus, ut ornare nisi viverra at. Phasellus eget lectus sem. Duis faucibus diam justo, mollis rhoncus104
eros tincidunt nec. Sed ultricies nisl urna, a lacinia velit commodo eget.105

$\eta$ is 'azimuth time' 

$$
f_{\eta_c} = -\frac{2}{\lambda} \left . \frac{d R(\eta)}{d \eta } \right | _{\eta=\eta_c}
$$

$$
f_{\eta_c} = \frac{2 V_r \sin \theta}{\lambda}
$$


Doppler frequency shift is related to the derivative of phase with respect to slow time
$$
\Delta f = \frac{1}{2\pi}\frac{\partial \phi}{\partial \tau} = -\frac{2}{\lambda} \frac{\partial r}{\partial \tau}
$$
or the Doppler wavenumber related to the along-track position
$$
\nu = -\frac{2}{\lambda} \frac{\partial r}{\partial x}
$$
taking the derivative
$$
\nu = -\frac{2}{\lambda} \frac{x}{r}
$$
which is approximately
$$
f_{\eta_c} = \frac{2}{\lambda} \sin \theta
$$


```{math}
:label: Doppler-squint
\frac{\partial \phi}{\partial x}
```

$$
\frac{\partial \phi}{\partial x}
$$

$$
\frac{\partial r}{\partial x}
$$

$$
\tan\theta = \frac{x-x_0}{h+d/n}
$$

The point at which this 

## Compiling a multisquint mosaic

Integer justo tortor, auctor id mi et, hendrerit mollis est. Cras laoreet diam augue, eu semper ipsum luctus98
in. Phasellus lacinia enim libero, sed gravida tortor ultricies ut. Cras consequat at tortor et egestas. Etiam99
scelerisque et lacus et eleifend. Aenean mattis ligula at bibendum pellentesque. Pellentesque facilisis velit100
velit, et elementum quam faucibus sed. Praesent sodales lorem ac pellentesque dapibus. Morbi rutrum est101
vel interdum tincidunt. Proin ac nulla libero. Proin metus arcu, commodo tempor vestibulum vel, posuere102
eget diam. Sed scelerisque lorem sed libero tristique, eget aliquet augue fermentum. Aenean malesuada103
justo lectus, ut ornare nisi viverra at. Phasellus eget lectus sem. Duis faucibus diam justo, mollis rhoncus104
eros tincidunt nec. Sed ultricies nisl urna, a lacinia velit commodo eget.105

```{figure} ./figures/squint-flowchart.png
---
height: 200px
name: multi-squint-flowchart
---
Algorithm design
```

Integer justo tortor, auctor id mi et, hendrerit mollis est. Cras laoreet diam augue, eu semper ipsum luctus98
in. Phasellus lacinia enim libero, sed gravida tortor ultricies ut. Cras consequat at tortor et egestas. Etiam99
scelerisque et lacus et eleifend. Aenean mattis ligula at bibendum pellentesque. Pellentesque facilisis velit100
velit, et elementum quam faucibus sed. Praesent sodales lorem ac pellentesque dapibus. Morbi rutrum est101
vel interdum tincidunt. Proin ac nulla libero. Proin metus arcu, commodo tempor vestibulum vel, posuere102
eget diam. Sed scelerisque lorem sed libero tristique, eget aliquet augue fermentum. Aenean malesuada103
justo lectus, ut ornare nisi viverra at. Phasellus eget lectus sem. Duis faucibus diam justo, mollis rhoncus104
eros tincidunt nec. Sed ultricies nisl urna, a lacinia velit commodo eget.105