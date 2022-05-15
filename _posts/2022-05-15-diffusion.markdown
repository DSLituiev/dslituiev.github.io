# Diffusion Probability Models behind DALL-E2
I decided to write this blog while on my stay in Switzerland because (1) DALL-E2 is cool and I wanted to understand it better (2) I studied diffusion modelling here a while ago and I believe diffusion is a cool model, and (3) the ideas behind this paper I cool but need some time to understand and I wanted to share my path to getting it.

# Diffusion equation
Diffusion in physical world is a process of mass redistribution in the space due to stochastic motion. Classically one talks about random walk due to thermal motion of particles (including molecules). One can also think of other instances or models of diffusion due to undirected motion like animals wondering around. That's a microscopic view of diffusion. One of the first works on connecting microscopic view to the macroscopic was done by Albert Einstein in 1905. He looks at one-dimensional displacement of a liquid-suspended particle. One of the achievements of his paper is formulation of a partial-differential equation (PDE) for diffusion. Einstein achieves it by contemplating infinitesimal changes of particle concentrations across space and time. The classical (linear homogenous) diffusion equation thus reads:

<img src="https://render.githubusercontent.com/render/math?math=\frac{\partial}{\partial t} u = D \cdot \frac{\partial^2}{\partial x^2} u">

That is an infinitesimal time-dependent change in concentration will be proportional to its second-order spatial gradient (Laplacian). Another way to look at this equation is by putting all the differential terms in the left hand side:

<img src="https://render.githubusercontent.com/render/math?math=\left[ \frac{\partial}{\partial t} - D \cdot \frac{\partial^2}{\partial x^2} \right] u = 0">


If we had a source of the substance (or particles) $f(x)$, we can set left-hand side to it:

<img src="https://render.githubusercontent.com/render/math?math=\left[ \frac{\partial}{\partial t} - D \cdot \frac{\partial^2}{\partial x^2} \right] u = f(x,t)">


# Solving the Diffusion Equation

Here is one ways to show that a solution to diffusion equation can be presented as a convolution operator. 
We will do it by using [Green's functions](https://en.wikipedia.org/wiki/Green%27s_function). A Green's function is a function that our operator of interest squashes to a [Dirac delta function](https://en.wikipedia.org/wiki/Dirac_delta_function):

<img src="https://render.githubusercontent.com/render/math?math=L [G(x, t)] = \delta(x, t)">

In our case <img src="https://render.githubusercontent.com/render/math?math=L = \frac{\partial}{\partial t} - D \cdot \frac{\partial^2}{\partial x^2}">

After we find a Green function to our problem, the promise is that we will be able to obtain a solution by convolving our source (or sink) function:

<img src="https://render.githubusercontent.com/render/math?math=u(x,t) = G(.,.) * f(x,t)">

A solution is a generalized (i.e. discontinuous) Green function. In our case, we shall assume an inifinite-space domain, i.e. our space is not confined and thus the substance won't be accumulating within some boundaries.
For these conditions we will have:

<img src="https://render.githubusercontent.com/render/math?math=\Theta(t)\left(\frac{1}{4\pi kt}\right)^{d/2} e^{-r^2/4kt}">, where *d* is the dimensionality of the space (e.g. d=3 for 3D).

<img src="https://render.githubusercontent.com/render/math?math=r=\sqrt{x^{2} + y^{2} + z^{2}}">

Thus the equation can be solved by convolving the initial distribution $u$ with a causal, "forward-looking" Gaussian as signified by a time-dependent Heaviside function multiplier.
Note: this Heaviside function looks like a funny formal requirement now. Why would one integrate for times befor $t=0$? We will get to this later.

## Diffusion Applied to Images

There are two ways to apply diffusion to images, and for the purpose of the Generative Diffusion models, we are interested in only one of them. 
One that may be least intuitive.
These two ways differ by what we treat as a spatial coordinate.

### Diffusion as Gaussian Blur
This is not the way how Generative Diffusion Models are concerned with diffusion on images, but this is a classical example you might have learned, so let's clear it out of the way.
Here we deal with a 2D space, with our *x* and *y* coordinates being the image width and height coordinates.
Our equivalent of "concentration" is pixel intensities *C(x, y)*.
In this case, diffusion will be performed by applying a well known Gaussian filter *G(x-., y-.)*.

### Diffusion on Pixel Intensities
This is the way Generative Diffusion Models treats images. Here our dimensions are plain pixel indices. 
E.g. if we have a 2*2 image, easiest way to think about the variables is as x_1, x_2, x_3, x_4.
Or more generally, for an H*W (for height by width) image, we will have variables x_1 through x_{H*W}.

Diffusion won't be preserving concentration by smearing it to neighboring pixels. Instead, think of an image as a particle in this H*W space.



