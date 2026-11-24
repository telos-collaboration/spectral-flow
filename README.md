# Adjoint Wilson spectral flow

`spectral-flow` is an adaptation of `Grid/tests/lanczos/Test_wilson_specflow.cc` for SU(2) gauge theory with two adjoint fermions.

It retains the original calculation: filtering $D_W^\dagger D_W$, then using $H_W = \gamma_5 D_W$ to reconstruct eigenvalues and check convergence. Eigenvalues and the real and imaginary parts of chirality are printed to standard output.

The adaptation adds command-line XML input, a loop over saved configurations, and XML controls for the spectral scan and Lanczos parameters. The code can be adapted to other gauge theories and fermion representations by updating the relevant implementation and recompiling against the appropriate version of Grid.

## Build

```sh
./bootstrap.sh
mkdir -p build
cd build
../configure --with-grid=/path/to/Grid/install
make -j2
```

The application uses Grid's SU adjoint types directly (`WilsonAdjFermionD`
and `AdjointRepresentation`); use an Nc=2 Grid installation for SU(2).

## Input and run

Edit `par-example.xml`, then from `build` run:

```sh
./spectral_flow ../par-example.xml --grid 16.16.16.32 --mpi 1.1.1.1 > spectral-flow.log 2>&1
```

XML parameters:

- `M5_start`, `M5_end`, `M5_step`: ascending scan, Wilson mass = **-M5**.
  The endpoint is included if it lies on the step sequence.
- `Nstop`, `Nk`, `Np`, `MaxIt`, `resid`: the original Grid IRL parameters;
  maximum basis size is `Nm = Nk + Np`.
- `ChebyLow`, `ChebyHigh`, `ChebyOrder`: original Chebyshev constructor arguments,
  applied to D_W dagger D_W. Order means degree + 1 in this Grid implementation.
  The example filter settings are starting values, not automatically tuned.
- `boundary_conditions`: four `<elem>` entries in x,y,z,t order, +1 periodic
  or -1 antiperiodic. All periodic reproduces the original test's default.
- `rng_seeds`: Grid RNG seed vector. The example reproduces the original seeds.
