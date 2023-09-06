# featom: Finite Element Solvers for Atomic Structure Calculations

This library implements accurate and efficient radial Schrödinger and Dirac
finite element solvers. The formulation admits general potentials and meshes:
uniform, exponential, or other. Additionally, a squared Hamiltonian approach has
been used for the Dirac equation, which eliminates spurious states.

## Article

Detailed description of methods, convergence studies and implementation details
may be found in the following article:

> Čertík, Ondřej, et al. High-Order Finite Element Method for Atomic Structure Calculations. arXiv, 11 July 2023. arXiv.org, http://arxiv.org/abs/2307.05856.

## Accuracy

With the provided meshes, the solvers (both Schrödinger and Dirac) can converge
to at least 1e-8 Ha accuracy (with double precision of approximately 16
significant digits) for all eigenvalues and total DFT energies for all atoms up
to uranium (Z=92).

The converged nonrelativistic and relativistic results agree with
[dftatom](https://github.com/certik/dftatom) to
1e-8 Ha accuracy, and with the NIST benchmarks to the stated accuracy of those
benchmarks (2e-6 Ha in eigenvalues and 1e-6 Ha in total energies).

http://physics.nist.gov/PhysRefData/DFTdata/Tables/ptable.html

The accuracy is on par with [dftatom](https://github.com/certik/dftatom/) and
uses significantly less computationally expensive routines.

## Compilation
<!-- TODO: Make bindings and document with Sphinx -->
This program is packaged with `fpm`, the [Fortran package manager](https://fpm.fortran-lang.org/).

``` bash
# All of these can be passed the --profile=release flag
fpm build
fpm test
fpm run --profile=release conv -- 0 0 5
```

Where the parameters to `conv` are:

```fortran
! <study_type> can be,
!       0: error as p is varied
!       1: error as rmax is varied
!       2: error as Ne is varied
!
! <equation> can be,
!       0: Schroedinger
!       1: Dirac
!
! For <study_type>
!       0, 1: 3rd parameter = Ne (Number of elements)
!       2   : 3rd parameter = p  (Polynomial order)
```

### Setting up

We can use an `anaconda` helper like `micromamba` (installation instructions [are here](https://mamba.readthedocs.io/en/latest/installation.html).

We can now set up the tools needed. We support both `fpm` and `meson` as build systems.

``` bash
# Global
micromamba install fpm meson  -c conda-forge
# Project Local
micromamba create -p ./tmp fpm meson -c conda-forge
micromamba activate ./tmp
# Optionally: blas lapack openmp gfortran
# Best obtained with a package manager
# Alternative
micromamba create -f environment.yml # creates fe
micromamba activate fe
```

#### Using `fpm`

``` bash
fpm build
fpm run --profile=release conv -- 0 0 5
```

#### Using `meson`

``` bash
# release mode is the default
meson setup bbdir -Dwith_app=true
./bbdir/app/conv 0 0 5
```

## Testing

Tests can be run by:
```
fpm test
# Or, changing to debug
meson setup bbdir -Dwith_tests=true --buildtype=debug
meson test -C bbdir
1/8 CoulombSchroed         OK              0.05s
2/8 DftSchroedFast         OK              0.06s
3/8 HarmonicSchroed        OK              0.11s
4/8 DftSchroed             OK              0.12s
5/8 HarmonicDirac          OK              0.47s
6/8 CoulombDirac           OK              0.49s
7/8 DftDiracFast           OK              1.95s
8/8 DftDirac               OK              6.45s

Ok:                 8   
Expected Fail:      0   
Fail:               0   
Unexpected Pass:    0   
Skipped:            0   
Timeout:            0   
```

Individual test binaries can also be executed, e.g.:

``` bash
meson compile -C bbdir
./bbdir/testDftDirac
```

## Documentation

The API documentation is generated by [doxygen](https://www.doxygen.nl/index.html) with the [doxyYoda](https://github.com/HaoZeke/doxyYoda) theme.

To build a local copy and serve it consider:

``` bash
bash scrpits/mkdoxydoc.sh
```


## License

This program is MIT licensed, see the LICENSE file for details.
