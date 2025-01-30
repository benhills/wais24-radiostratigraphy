# Introduction

<!--- high-level radar sounding and englacial layers -->
<!---
Radio-echo sounding (RES) is commonly used to survey ice masses \citep{Schroeder2020FiveRadioglaciology}, with targets of both the ice-bed interface \citep{Fremand2023AntarcticData} and englacial layers \citep{Bingham2024ReviewSheets,Macgregor2015Radiostratigraphy}.
Englacial radio echoes are reflected by conductivity or permittivity contrasts, most commonly associated with changes in the ice impurity content \citep{Eisen2003RevealingModeling, gudmandsen1975layer}.
Those impurity contrasts are deposited as continuous horizons with annual or inter-annual variations in the snow deposition.
Since the layers are continuous and uniform, they form a set of coherent reflections comprising the "radiostratigraphy".-->
Ice is largely transparent to radio waves, so radio-echo sounding (RES) is a unique glaciological surveying tool that gives a view inside ice sheets at continental scale.
Radar echoes from the ice bottom are used to survey ice-sheet geometry for constraints on the total ice volume {cite}`Fremand2023AntarcticData`.
Substantially more information is preserved *within* the ice column, where englacial echoes are created by contrasts in conductivity or permittivity. 
When englacial echoes are coherent between traces, they form continuous horizontal layers across the image, a ''radiostratigraphy''. 
The continuous layers are commonly interpreted to be of equal age and are used to date ice across the large spatial scales between ice cores {cite}`Cavitte2021AYears`, as is the goal of AntArchitecture, an action group for the Scientific Committee on Antarctic Research {cite}`Bingham2024ReviewSheets`. 
Continuous layers can also be distorted by ice flow, so present-day stratigraphy preserves a history of ice dynamics from which we can infer both past flow {cite}`Conway2002SwitchStream` and dynamic change through the transition {cite}`Siegert2013LateAntarctica`.

<!--- layer disruptions; recrystalization, birefringence, end with open ended 'vertical disruptions' -->
<!-- Although deposited uniformly, englacial layers are advected with the ice as it flows, and can therefore be strained, disrupted, or even destroyed entirely.
For that reason, an inferred continuity of englacial layers is commonly used to assess change in dynamic areas \citep{Karlsson2012ALayers}.
Extraordinary ice properties can also disrupt the radiostratigraphy, although less directly.
For example, radar birefringence in anisotropic ice creates a "beat signature" in the received power, obscuring signal reflected from layers at a regular frequency \citep{Young2021InferringAntarctica}.
Additionally, recrystallization in warm ice \citep{mutter-2024-2450,Lilien2021BriefC} or entrained sediment/water \citep{} may lead to incoherent volume scattering in basal ice which, again, disrupts the radiostratigraphy.-->
Observed englacial layers, however, are not always continuous, which then limits or wholly prevents interpretations for ice-sheet geometry, process, and dynamic history.
The stratigraphy can be disrupted by cross-cutting reflectors such as fractures and unconformities {cite}`Kingslake2018ExtensiveHolocene`.
There are also cases, the focus of this article, where the imaged radiostratigraphy may be poorly resolved even when there is no explicit physical disruption in the englacial layers: i) birefringence in anisotropic ice can cause the polarized wave to rotate in and out of alignment with the receive antenna {cite}`Young2021InferringAntarctica`; and ii) destructive interference due to assumptions built into standard processing algorithms (i.e., the focusing aperture) can reduce the representative power in the processed RES image, especially for dipping layers {cite}`holschuh2014power`.

<!--- vertical disruptions in detail -->
<!--One particular classification of radiostratigraphic disruption (Figure \ref{fig:1-whirlwind-examples})
\cite{Holschuh2014PowerRadar}
\cite{Winter2015AirborneDynamics}
\cite{Franke2022AirborneStream}
\cite{Karlsson2009TheData}-->
Englacial layers in an ice sheet are generally smooth relative to the transmitted radar wavelength, so they create a specular reflection, where most energy is reflected at the incident angle.
For a perfectly horizontal layer and a nadir-looking radar *all* the reflected energy is directed back to the receiver.
Some layers are approximately this way, deposited uniformly as a slight chemical or density anomaly at the ice surface and buried over time.
For this reason, near-surface layers in the accumulation area are extraordinarily well-resolved and can be connected over 100s of km between ice cores {cite}`Cavitte2021AYears`{cite}`Bodart2021Age-DepthChronology`.
In other settings, layers are not flat; for example, near the glacier bed layers will begin to conform with the bed topography {cite}`Hudleston2015StructuresReview`, even if that is steep or rough.
On the other hand, in highly dynamic areas layers may *not* conform with the bed, often being distorted by the ice flow {cite}`Holschuh2019ThermalMargins`{cite}`Karlsson2012ALayers` or mass exchange at the bed {cite}`Jordan2018AnomalouslyPole`{cite}`Bell2011WidespreadBase`.
In any case, layer dips can steepen significantly compared to the flat ice-sheet surface.
Then, a *dipping* specular layer may be resolvable only by some off-nadir propagating wave, orthogonal to the layer dip.

<!-- incoherent and coherent processing to increase signal of layers
\cite{Castelletti2019LayerData,Heister2018CoherentData,Ferro2019SquintedEnhancement,Rodriguez2009MultiSquintRS}
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
High fidelity, algorithmically acquired layer dips across full survey campaigns can be used to guide phase-based layer tracking {cite}`Macgregor2015Radiostratigraphy`, or as we propose here to interpret the ice dynamics and ice-sheet history directly.-->

Here, we...

