# Frequently asked questions about Aurora

## I need LAPACK and BLAS - they used to be in /usr/lib64
Using an optimised BLAS library is important for achieving good performance.  The libraries in `/usr/lib64` are typically not highly optimised.  We currently support [OpenBLAS](http://www.openblas.net/) within the foss [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) and [MKL](https://software.intel.com/en-us/intel-mkl) in the intel, iomkl and gimkl toolchains.  Both libraries show excellent performance when e.g. used to build HPL (high performance linpack).

## I need an ACML module
ACML is a highly optimised library which offeres BLAS functionality among other things.  It is provided by AMD to work with the AMD processors, like the once deployed on Alarik.  Since Aurora utilises INTEL processors using ACML is no longer an option.  On Aurora we offer OpenBLAS and MKL instead of ACML.  Please refer to the question: [*I need LAPACK and BLAS*](#i-need-lapack-and-blas) for more details.
