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

# Multi-squint focusing

Following the methods in {doc}`background`, we process a radar image at a range of squint angles.
Integer justo tortor, auctor id mi et, hendrerit mollis est. Cras laoreet diam augue, eu semper ipsum luctus98
in. Phasellus lacinia enim libero, sed gravida tortor ultricies ut. Cras consequat at tortor et egestas. Etiam99
scelerisque et lacus et eleifend. Aenean mattis ligula at bibendum pellentesque. Pellentesque facilisis velit100
velit, et elementum quam faucibus sed. Praesent sodales lorem ac pellentesque dapibus. Morbi rutrum est101
vel interdum tincidunt. Proin ac nulla libero. Proin metus arcu, commodo tempor vestibulum vel, posuere102
eget diam. Sed scelerisque lorem sed libero tristique, eget aliquet augue fermentum. Aenean malesuada103
justo lectus, ut ornare nisi viverra at. Phasellus eget lectus sem. Duis faucibus diam justo, mollis rhoncus104
eros tincidunt nec. Sed ultricies nisl urna, a lacinia velit commodo eget.105

```{figure} ./figures/multisquint-movie.gif
---
height: 400px
name: multi-squint-movie
---
Image processed across a range of squint angles.
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


Doppler frequency is related to the derivative of range with respect to slow time
$$
f = -\frac{2}{\lambda} \frac{\partial r}{\partial \tau}
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
f_{\eta_c} = \frac{2}{\lambda} (\sin \theta + n\sin \theta_{ice})
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

## Compiling an multisquint mosaic

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

```{figure} ./figures/multisquint-result.png
---
height: 400px
name: multi-squint-result
---
Mosaic result.
```

Integer justo tortor, auctor id mi et, hendrerit mollis est. Cras laoreet diam augue, eu semper ipsum luctus98
in. Phasellus lacinia enim libero, sed gravida tortor ultricies ut. Cras consequat at tortor et egestas. Etiam99
scelerisque et lacus et eleifend. Aenean mattis ligula at bibendum pellentesque. Pellentesque facilisis velit100
velit, et elementum quam faucibus sed. Praesent sodales lorem ac pellentesque dapibus. Morbi rutrum est101
vel interdum tincidunt. Proin ac nulla libero. Proin metus arcu, commodo tempor vestibulum vel, posuere102
eget diam. Sed scelerisque lorem sed libero tristique, eget aliquet augue fermentum. Aenean malesuada103
justo lectus, ut ornare nisi viverra at. Phasellus eget lectus sem. Duis faucibus diam justo, mollis rhoncus104
eros tincidunt nec. Sed ultricies nisl urna, a lacinia velit commodo eget.105