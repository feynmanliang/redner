# redner

News

08/07/2019 - Fixed a roughness texture bug.  
07/27/2019 - Tensorflow 1.14 support! Currently only support eager execution. We will support graph execution after tensorflow 2.0 becomes stable. See tests_tensorflow for examples (I recommend starting from tests_tensorflow/test_single_triangle.py). The CMake files should automatically detect tensorflow in python and install corresponding files. Tutorials are work in progress. Lots of thanks go to [Seyoung Park](https://github.com/SuperShinyEyes) for the contribution!  
06/25/2019 - Added orthographic cameras (see examples/two_d_mesh.py).  
05/13/2019 - Fixed quite a few bugs related to camera derivatives. If something didn't work for you before, maybe try again.  
04/28/2019 - Added QMC support (see tests/test_qmc.py and the documentation in pyredner.serialize_scene()).  
04/01/2019 - Now support multi-GPU (see pyredner.set\_device).  
03/31/2019 - Brought back the hierarchical edge sampling method in the paper.  
02/02/2019 - The [wiki](https://github.com/BachiLi/redner/wiki) now contains a series of tutorial. The plan is to further expand the examples.  

![](https://people.csail.mit.edu/tzumao/diffrt/teaser.jpg)

redner is a differentiable Monte Carlo renderer that can take the derivatives of rendering output with respect to arbitrary 
scene parameters, that is, you can backpropagate from the image to your 3D scene. One of the major usages of redner is inverse rendering (hence the name redner) through gradient descent. What sets redner apart is that it is physically-based -- which means it simulates photons and produce realistic lighting phenomena, such as shadow and global illumination, and it handles the derivatives of these features correctly. You can also use redner in a [fast deferred rendering mode](https://github.com/BachiLi/redner/wiki/Tutorial-4%3A-fast-deferred-rendering) for local shading: in this mode it still has correct gradient estimation and more elaborate material models compared to most differentiable renderers out there.

For more details on the renderer, what it can do, and the techniques it use for computing the derivatives, please
take a look at the paper:
"Differentiable Monte Carlo Ray Tracing through Edge Sampling", Tzu-Mao Li, Miika Aittala, Fredo Durand, Jaakko Lehtinen
[https://people.csail.mit.edu/tzumao/diffrt/].  
Since the submission we have improved the renderer a bit. In particular we implemented a CUDA backend and accelerated
the continuous derivatives significantly by replacing automatic differentiation with hand derivatives. See Tzu-Mao Li's [thesis](https://people.csail.mit.edu/tzumao/phdthesis/phdthesis.pdf) for even more details.

redner is expected to be used with [PyTorch](https://pytorch.org/), and can be used seamlessly with PyTorch operators. A good starting point to learn how to use redner is to look at the [wiki](https://github.com/BachiLi/redner/wiki).
While the documentation is work in progress, you can take a look at the [tests directory](tests) to have some ideas.
redner inherits a subset of [Mitsuba](http://www.mitsuba-renderer.org) scene format,
see [tests/test_teapot_reflectance.py](https://github.com/BachiLi/redner/blob/master/tests/test_teapot_reflectance.py) and [tests/test_teapot_specular.py](https://github.com/BachiLi/redner/blob/master/tests/test_teapot_specular.py) for examples of loading Mitsuba scene files. There is also a Wavefront obj file loader for individual meshes, take a look at [tutorials/02_pose_estimation.py](https://github.com/BachiLi/redner/blob/master/tutorials/02_pose_estimation.py).

redner depends on a few libraries/systems:
- [Python 3.6 or above](https://www.python.org) (required)
- [pybind11](https://github.com/pybind/pybind11) (required)
- [PyTorch 1.1](https://pytorch.org) (required)
- [Tensorflow 1.14](https://www.tensorflow.org/) (optional, required if PyTorch is not installed)
- [OpenEXR](https://github.com/openexr/openexr) (required)
- [Embree](https://embree.github.io) (required)
- [CUDA 10](https://developer.nvidia.com/cuda-downloads) (optional, need GPU at Kepler class or newer)
- [optix prime](https://developer.nvidia.com/optix) (optional, required when compiled with CUDA)
- [miniconda](https://conda.io/miniconda.html) (optional, but recommended)
- [OpenEXR Python](https://github.com/jamesbowman/openexrpython) (required, included in a submodule)
- [Thrust](https://thrust.github.io) (required, included in a submodule)
- [miniz](https://github.com/richgel999/miniz) (already in this repository)
- A few other python packages: numpy, scikit-image

I recommend using conda to setup the Python related dependencies, e.g.:
```
conda install pybind11
conda install pytorch -c pytorch
```

redner uses [CMake](https://cmake.org) as its build system. You need CMake 3.12 or above to build redner.
The build procedure follows common CMake instructions. See [wiki](https://github.com/BachiLi/redner/wiki/Installation) for installation guide.

See [here](https://github.com/BachiLi/redner/pull/11) for build instruction on Windows.

redner is tested under MacOS with clang 7 and Ubuntu with gcc 7. In general any compiler with c++14 support should work.

The current development plan is to enhance the renderer. Following features will be added in the near future (not listed in any particular order):
- More BSDFs e.g. glass/GGX
- Support for edge shared by more than two triangles
  (The code currently assumes every triangle edge is shared by at most two triangles.
   If your mesh doesn't satisfy this, you can preprocess it in other mesh processing softwares such as [MeshLab](http://www.meshlab.net))
- Source-to-source automatic differentiation
- Improve mipmapping memory usage, EWA filtering, covariance tracing
- Russian roulette
- Distribution effects: depth of field/motion blur
- Proper pixel filter (currently only support 1x1 box filter)
- Mini-batching
- Volumetric path tracing (e.g. [http://www.cs.cornell.edu/projects/translucency/#acquisition-sa13](http://www.cs.cornell.edu/projects/translucency/#acquisition-sa13))
- Spectral rendering
- Backface culling
- Install script
- Documentation

If you have any questions/comments/bug reports, feel free to open a github issue or e-mail to the author
Tzu-Mao Li (tzumao@mit.edu)
