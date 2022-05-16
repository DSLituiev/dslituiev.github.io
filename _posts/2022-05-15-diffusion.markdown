# Diffusion Probability Models behind DALL-E2
I decided to write this blog while on my stay in Switzerland because (1) DALL-E2 is cool and I wanted to understand it better (2) a while ago I studied diffusion in my grad school here in Switzerland and I think it's a cool topic, and (3) the ideas behind this paper I cool but need some time to understand and I wanted to share my path to getting it.

# References
1. Sohl-Dickstein, Weiss, Maheswaranathan, Ganguli (2015) Deep Unsupervised Learning using Nonequilibrium Thermodynamics, [Arxiv](https://arxiv.org/pdf/1503.03585.pdf)
2. Ho, Jain, Abbeel (2020) Denoising Diffusion Probabilistic Models, [Arxiv](https://arxiv.org/pdf/2006.11239.pdf)
3. Dhariwal and Nichol (2021) Diffusion Models Beat GANs on Image Synthesis,  [Arxiv](https://arxiv.org/pdf/2105.05233.pdf)

# Notation

I will be using partial derivatives and occasionally I will use mapping notation where *f(x) = y* can be re-written as 
<img src="https://render.githubusercontent.com/render/math?math=f \, : x \rightarrow y">, which reads "function *f* maps *x* to *y*.  

# Diffusion equation
Diffusion in physical world is a process of mass redistribution in the space due to stochastic motion. Classically one talks about random walk due to thermal motion of particles (including molecules). One can also think of other instances or models of diffusion due to undirected motion like animals wondering around. That's a microscopic view of diffusion. One of the first works on connecting microscopic view to the macroscopic was done by Albert Einstein in 1905. He looks at one-dimensional displacement of a liquid-suspended particle. One of the achievements of his paper is formulation of a partial-differential equation (PDE) for diffusion. Einstein achieves it by contemplating infinitesimal changes of particle concentrations across space and time. The classical (linear homogenous) diffusion equation thus reads:

<img src="https://render.githubusercontent.com/render/math?math=\frac{\partial}{\partial t} u = D \cdot \frac{\partial^2}{\partial x^2} u %2B f(x,t)">

Where *f(x,t)* is a function that describes the source of the matter. 

That is an infinitesimal time-dependent change in concentration will be proportional to its second-order spatial gradient (Laplacian). Another way to look at this equation is by putting all the differential terms in the left hand side:

<img src="https://render.githubusercontent.com/render/math?math=\left[ \frac{\partial}{\partial t} - D \cdot \frac{\partial^2}{\partial x^2} \right] u = L [u] = f(x,t)">

One can also think of an inverse operator <img src="https://render.githubusercontent.com/render/math?math=L ^{-1} \, : f(x,t) \rightarrow u(x,t)"> as one that maps a source *f(x,t)* to a distribution of the matter *u(x,t)*.

Additionally, boundary conditions need to be specified, but we shall omit them for now.

# Solving the Diffusion Equation

Here is one ways to show that a solution to diffusion equation can be presented as a convolution operator. 
We will do it by using [Green's functions](https://en.wikipedia.org/wiki/Green%27s_function). A Green's function is a function that our operator of interest squashes to a [Dirac delta function](https://en.wikipedia.org/wiki/Dirac_delta_function):

<img src="https://render.githubusercontent.com/render/math?math=L [G(x, t)] = \delta(x, t)">

Or in other words, Green function is an *impulse response function* [a natural-domain counterpart of a *transfer function*](https://en.wikipedia.org/wiki/Transfer_function) of diffusion, i.e., a distribution that diffusion creates out of a Dirac delta spike:

<img src="https://render.githubusercontent.com/render/math?math=L^{-1} \,: \delta(x, t) \rightarrow G(x, t)">


In our case <img src="https://render.githubusercontent.com/render/math?math=L = \frac{\partial}{\partial t} - D \cdot \frac{\partial^2}{\partial x^2}">

Green function allows one to obtain a solution by convolution with the source (or sink) function:

<img src="https://render.githubusercontent.com/render/math?math=u(x,t) = G(.,.) * f(x,t)">

A solution is a generalized (i.e. discontinuous) Green function. In our case, we shall assume an inifinite-space domain, i.e. our space is not confined and thus the substance won't be accumulating within some boundaries.
For these conditions we will have:

<img src="https://render.githubusercontent.com/render/math?math=H(t)\left(\frac{1}{4\pi kt}\right)^{d/2} e^{-r^2/4kt}">

where *d* is the dimensionality of the space (e.g. *d=3* for 3D).

<img src="https://render.githubusercontent.com/render/math?math=r = \sqrt{x^{2} %2B y^{2} %2B z^{2}}">

Thus the equation can be solved by convolving the initial distribution *u* with a causal, "forward-looking" Gaussian as signified by a time-dependent Heaviside function multiplier.
Note: this Heaviside function looks like a funny formal requirement now. Why would one integrate for times before *t=0*? We will get to this later.

## Diffusion Applied to Images

There are two ways to apply diffusion to images, and for the purpose of the Generative Diffusion models, we are interested in only one of them. 
One that may be least intuitive.
These two ways differ by what we treat as a spatial coordinate.

### Diffusion as Gaussian Blur over Pixel Intensities within an Image
This is **not** the way how Generative Diffusion Models are concerned with diffusion on images, but this is a classical example you might have learned, so let's clear it out of the way.
Here we deal with a 2D space, with our *x* and *y* coordinates being the image width and height coordinates.
Our equivalent of "concentration" is pixel intensities *C(x, y)*.
In this case, diffusion will be performed by applying a well known Gaussian filter *G(x-., y-.)*, which will blur the "dye".

### Diffusion on Images within an Image Space
This is the way Generative Diffusion Models treats images. Here our dimensions are plain pixel indices. 
E.g. if we have a 2×2 image, easiest way to think about the variables is as 
<img src="https://render.githubusercontent.com/render/math?math=x_1, x_2, x_3, x_4">.

Or more generally, for an H×W (for height by width) image, we will have variables 
<img src="https://render.githubusercontent.com/render/math?math=x_{1}"> through 
<img src="https://render.githubusercontent.com/render/math?math=x_{H \times W}">.

Diffusion in this sense is not about smearing pixel intensities on H×W 2D canvas.
Instead, think of each image as one particle in this H×W space. 
Or H×W×C space. This can be, for example, a space of all possible 128×128 RGB images with dimensionality of 128×128×3 = 49152. 

Thus we will discuss not the concentration of dye within each pixel, but instead displacement of image-particles around image-space and at times about concentration of images.

What does this notion of concentration of images entail? Think of all possible combinations of pixel intensities. Obviously not all of them are valid images. Most of them will look like "noise" to a human eye. Or as something "unnatural". But some points in image space and area around them are valid natural images, or something we would recognize as a painting or a piece of art.

The task of our generative model *G* is to find a location within a "valid" manifold (think of them as islands) when thrown into the vast image space:

<img src="https://render.githubusercontent.com/render/math?math=G: \mathbb{R}^D (\mathrm{seed}) \rightarrow \mathbb{R}^D (\mathrm{valid})">

where both images live in the same space: the "valid" image (observed or generated -- the task is to match these distributions)

<img src="https://render.githubusercontent.com/render/math?math=x_0 ~ \mathbb{R}^D">

and the latent random image, which we take as a seed for generation:

<img src="https://render.githubusercontent.com/render/math?math=x_T ~ \mathbb{R}^D">

or in terms of probability densities in the image space:

<img src="https://render.githubusercontent.com/render/math?math=q(x_0) = q(x_T) \cdot \displaystyle \prod q(x_{t-1}| x_t)">

Where each generation *G* is one of realizations or paths from the conditional probability distribution 
<img src="https://render.githubusercontent.com/render/math?math=\prod q(x_{t-1}| x_t)">.

## Learning a Diffusion Reversing Generator

The task of any generator model is being able to generate samples that look "real" or "valid" starting from a random seed. 
I'll use "valid" to make sure that unrealistic or unnatural i.e. art-like images "feel included".

To this end, as in most probabilistic generative models, the model is trained to maximize the log-likelihood of the model:

<img src="https://render.githubusercontent.com/render/math?math=\mathbb{E} \left[-\log P_\theta(x_0) \right]">

That is we want the model to assign high probabilities to "valid" samples. In the light of our intent of sampling from the model, we may also write it as

<img src="https://render.githubusercontent.com/render/math?math=\mathbb{E} \left[-\log P_\theta(\hat{x}_0) \right]">

The expectation is casted to be with respect to the data and forward-sampled latent variables (per encoder *q*).
That is we optimize the decoder based on observed data. A nice property is that our decoder and encoder are the same thing (that is they share same parameters). In other words, the model is reversible.

### A theoretical Note: Challenges of Diffusion Reversal on Noisy Data
So how does one undo diffusion? Strictly speaking there is no such a process in nature, although phase separation may be described as such to some degree.

Imagine putting a drop of dye in bathtub full of water and wating till it completely dissolves. Now, how can you tell where the drop was initially? If you don't let it dissolve completely you may be able to a degree. But the further you go, and the more noise you have (think someone pushing the bathtub), the harder it gets to undo it.

Theoretically it is known that reverse diffusion from a noisy input (i.e. object that has undergone diffusion with added noise) is an ill-posed problem.
Note that solving reverse diffusion without noise is not interesting or useful as we are interested in diversity of image generations.
This problem is known from literature on joint image denoising and optics correction, which arises for instance in microscopy.

In general, by definition an ill-posed task is such that has one or more of the following nasty properties:
- the task has no solution 
- the task has multiple solutions
- the solution is highly sensitive to the initial conditions (i.e. input)

In this case, solutions luckily exist, but we are facing the second and the third issues. 
That is there is no general-case undoing of diffusion. In order to undo diffusion, one needs a prior about what the initial object was.
We have that knowledge from our empirical distribution *q(x_0)* and we will be "baking in" the prior into our generator function by training it.

### Practical Aspects: Implementation of Diffusion Reversal on Noisy Data

An actual model component that will be performing denoising and reverse diffusion will be a [U-net (Ronneberger, Fischer, Brox, 2015) convolutional neural network](https://en.wikipedia.org/wiki/U-Net). The sampling and inference mechanism around it provides a probabilisticall tractable and optimal way to do exactly that: learning to extracts patterns out of noise. 

Going one step deeper, this is actually done other-way-around in this paper implementation: it is not the pattern that is extracted out of noise, but noise that is extracted out of noise. Yes, you read it correctly. We start with noise 
<img src="https://render.githubusercontent.com/render/math?math=\epsilon_T">
of ball-shaped covariance (that is all axes-pixels are seeded with noise of equal intensity). And then the model chisels away what it deems "extra" noise
<img src="https://render.githubusercontent.com/render/math?math=\hat{\epsilon_t}"> at each step to reveal the pattern. 

#### Markov chain

Additionally, the process is not a single-step noise-to-image. It uses a Markov chain *T*-depth iterative encoder-decoder. Luckily multiple variables can be calculated analytically. 


