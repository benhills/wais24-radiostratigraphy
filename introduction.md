# Introduction

<!--- high-level radar sounding and englacial layers -->
Ice is largely transparent to radio waves, so radio-echo sounding (RES) is a unique glaciological surveying tool that gives a view inside ice sheets at continental scale.
Radar echoes from the ice bottom are used to survey ice-sheet geometry for constraints on the total ice volume {cite}`Fremand2023AntarcticData`.
Substantially more information is preserved *within* the ice column, where englacial echoes are created by contrasts in conductivity or permittivity. 
When englacial echoes are coherent between traces, they form continuous horizontal layers across the image, a ''radiostratigraphy''. 
The continuous layers are commonly interpreted to be of equal age and are used to date ice across the large spatial scales between ice cores {cite}`Cavitte2021AYears`, as is the goal of AntArchitecture, an action group for the Scientific Committee on Antarctic Research {cite}`Bingham2024ReviewSheets`. 
Continuous layers can also be distorted by ice flow, so present-day stratigraphy preserves a history of ice dynamics from which we can infer both past flow {cite}`Conway2002SwitchStream` and dynamic change through the transition {cite}`Siegert2013LateAntarctica`.

<!--- layer disruptions -->
Observed englacial layers, however, are not always continuous, which then limits or wholly prevents interpretations for ice-sheet geometry, process, and dynamic history.
The stratigraphy can be disrupted by cross-cutting reflectors such as fractures and unconformities {cite}`Kingslake2018ExtensiveHolocene`.
There are also cases, the focus of this article, where the imaged radiostratigraphy may be poorly resolved even when there is no explicit physical disruption in the englacial layers: i) birefringence in anisotropic ice can cause the polarized wave to rotate in and out of alignment with the receive antenna {cite}`Young2021InferringAntarctica`; and ii) destructive interference due to assumptions built into standard processing algorithms (i.e., the focusing aperture) can reduce the representative power in the processed RES image, especially for dipping layers {cite}`holschuh2014power`.

<!--- vertical disruptions in detail -->
Englacial layers in an ice sheet are generally smooth relative to the transmitted radar wavelength, so they create a specular reflection, where most energy is reflected at the incident angle.
For a perfectly horizontal layer and a nadir-looking radar *all* the reflected energy is directed back to the receiver.
Some layers are approximately this way, deposited uniformly as a slight chemical or density anomaly at the ice surface and buried over time.
For this reason, near-surface layers in the accumulation area are extraordinarily well-resolved and can be connected over 100s of km between ice cores {cite}`Cavitte2021AYears`{cite}`Bodart2021Age-DepthChronology`.
In other settings, layers are not flat; for example, near the glacier bed layers will begin to conform with the bed topography {cite}`Hudleston2015StructuresReview`, even if that is steep or rough.
On the other hand, in highly dynamic areas layers may *not* conform with the bed, often being distorted by the ice flow {cite}`Holschuh2019ThermalMargins`{cite}`Karlsson2012ALayers` or mass exchange at the bed {cite}`Jordan2018AnomalouslyPole`{cite}`Bell2011WidespreadBase`.
In any case, layer dips can steepen significantly compared to the flat ice-sheet surface.
Then, a *dipping* specular layer may be resolvable only by some off-nadir propagating wave, orthogonal to the layer dip.

Here, we...
