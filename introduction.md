# Introduction

<!--- high-level radar sounding and englacial layers -->
Radio-echo sounding (RES) is commonly used to survey glaciers and ice sheets {cite}`Schroeder2020FiveRadioglaciology` because radio echoes from the ice-bed interface give an estimate of the total ice-sheet volume {cite}`Fremand2023AntarcticData`.
Additional information is preserved *within* the ice column, where englacial echoes are created by contrasts in conductivity or permittivity, typically associated with changes in the ice impurity content {cite}`Eisen2003RevealingModeling` {cite}`gudmandsen1975layer`.
Some of these englacial layers are smooth relative to the transmitted radar wavelength, deposited uniformly as continuous horizons with annual or inter-annual variations in the snow deposition and buried over time, forming a stratified set of coherent reflections comprising the "radiostratigraphy" {cite}`Macgregor2015Radiostratigraphy`.
Smooth layers create specular radar reflections, where most energy is reflected at the incident angle.
In the case of a perfectly specular horizontal layer and a nadir-looking radar *all* the reflected energy is directed back to the receiver.
For this reason, the relatively flat near-surface layers in the accumulation area of an ice sheet are extraordinarily well-resolved and can be connected over 100s of km between ice cores {cite}`Cavitte2021AYears` {cite}`Bingham2024ReviewSheets`. 

<!--- vertical disruptions in detail -->
In other glaciological settings, englacial layers are not flat; for example, near the glacier bed layers will begin to conform with the bed topography {cite}`Hudleston2015StructuresReview`, even if that is steep or rough.
In highly dynamic areas, layers may not be flat nor conform with the bed, often being distorted by the ice flow {cite}`Holschuh2019ThermalMargins`{cite}`Karlsson2012ALayers` or mass exchange at the bed {cite}`Jordan2018AnomalouslyPole`{cite}`Bell2011WidespreadBase`, and possibly preserving a history of paleo ice dynamics {cite}`Conway2002SwitchStream` {cite}`Siegert2013LateAntarctica`.
In any case, layer dips can steepen significantly compared to the flat ice-sheet surface.
Then, a *dipping* specular layer may be resolvable only by some off-nadir propagating wave, orthogonal to the layer dip.
For this reason, recieved radar power is often diminished for dipping layers {cite}`holschuh2014power`, sometimes creating anomalous vertical features within the RES image which have been called "tornadoes" or "whirlwinds" {cite}`Karlsson2009TheData` {cite}`Winter2015AirborneDynamics` ({numref}`reference-radargrams`).

```{figure} ./figures/ReferenceImages.png
---
height: 300px
name: reference-radargrams
---
[!!!PLACEHOLDER!!!] RES images from four distinct locations and measured by separate instruments, all with vertical disruptions in the radiostratigraphy; A) Thwaites Glacier {cite}`holschuh2014power`, B) Northeast Greenland Ice Stream {cite}`Franke2022AirborneStream`, Pine Island Glacier {cite}`Karlsson2009TheData`, Institute Ice Stream {cite}`Winter2015AirborneDynamics`.
```

<!-- coherent processing to increase signal of layers-->
The radar antennas used for sounding through ice have a small *real* aperture, meaning that the real beam width is large and some portion of the radar wave is directed off nadir {cite}`Heister2018CoherentData`.
It is therefore expected that radar power should be received even from dipping specular layers, but that destructive interference within the *synthetic* aperture can diminish power for those layers {cite}`holschuh2014power`.
The most commonly used synthetic aperture radar (SAR) processing algorithms are optimized for targets which are rough at the scale of the radar wavelength, scattering energy in all directions (diffuse), and are therefore observable from the many aspects across the synthetic aperture.
As an alternative, Castelletti et al. {cite}`castelletti2019layer` proposed a "layer-optimized" SAR (LOSAR) algorithm which stacks along a linear dip angle within the synthetic aperture (assuming perfect specularity).
LOSAR images have better-resolved radiostratigraphy compared to prior data products.
However, the LOSAR algorithm assumes that features are linear within the synthetic aperture which can lead to unrealistic abrupt angles in the processed image.

<!-- In this paper we will...-->
In this work, we outline an alternative SAR processing algorithm which uses a synthetic squint angle {cite}`Rodriguez2009MultiSquintRS` {cite}`Ferro2019SquintedEnhancement` to steer the radar beam off nadir and toward orthogonal incidince with a dipping englacial layer.
By using multiple squints within the same RES image, we compile a mosaic in which the signal-to-noise is maximized for all englacial targets.
We demonstate the effectiveness of the algorithm with an example from data acquired with the multi-channel coherent radar depth sounder (MCoRDS) at Academy Glacier, East Antarctica.
The improved result can be used for better interpretations of individual layers of interest and to generate more precise glaciological {cite}`sanderson2023englacial` {cite}`Catania2006SequentialAntarctica` and paleoclimatological {cite}`Lilien2021BriefC` {cite}`Bodart2021Age-DepthChronology` insight.