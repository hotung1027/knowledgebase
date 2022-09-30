# [[SideLobes]]
The weight across the phased array, alike an Fourier Transform of the weight window, the array radiates a pattern following a sinusoidal wave.
![](https://www.analog.com/-/media/images/analog-dialogue/en/volume-54/number-3/articles/phased-array-antenna-patterns-part3/306166-fig-01.png?h=270&hash=E38C24EB14A78BA01A7D046730903BEA&imgver=2)

# [[Tapering]]
Applying a weighting across the rectangular window, lowering the sidelobe of the beam pattern,
![](https://www.analog.com/-/media/images/analog-dialogue/en/volume-54/number-3/articles/phased-array-antenna-patterns-part3/306166-fig-02.png?h=270&hash=DFCB5722541DBAFE4D540EB6CB69C82E&imgver=2)
The unfortunate drawback of weighting is that sidelobes are reduced at the expense of widening the main lobe.

# [[Mutual Coupling Errors]]
The coupling between adjacent elements. 
The elements at the edge of the array have a different surrounding environment than the elements in the middle of the array. Furthermore, as the beam is steered, the mutual coupling between elements changes. All these effects create an additional error term to be accounted for by the antenna designer and, in practice, much effort is spent with electromagnetic simulators to characterize the radiation effects under these conditions.

# [[Beam Angle Resolution and Quantization Sidelobes]]

## Beam Angle Resolution
$$\theta = \sin^{-1}{\frac{\Delta \phi \lambda}{2\pi d}}$$
## Beam Angle Resolution related to phase shift
$$ \theta_{res} \approx \sin^{-1}{\frac{\phi LSB}{N \pi}} $$
