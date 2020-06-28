@def title = "Introduction"
@def hascode = true
@def date = Date(2020, 6, 16)
@def rss = "Introduction and review in AE623 notes."
@def tags = ["syntax", "code"]

\toc

# Linear Systems

## PLU factorization

```julia:./1_2_1_PLU.jl
using LinearAlgebra
A = [4 5 8 9; 6 9 21 19; 2 1 3 4; 4 11 16 12]
F = lu(A)
@show F
```

The output is

\output{./1_2_1_PLU.jl}

## Iterative smoothers

## Krylov subspace methods

There is a package [IterativeSolvers.jl](https://juliamath.github.io/IterativeSolvers.jl/dev/) that provides efficient iterative algorithms for solving large linear systems, eigenproblems, and singular value problems.
Most of the methods can be used matrix-free.

For example,

```julia:./1_2_3_GMRES.jl
using IterativeSolvers
A = rand(3,3)
A = A'*A
b = rand(3)
x = gmres(A,b)
println(x)
```

gives

\output{./1_2_3_GMRES.jl}

# Numerical Integration

## Basic polynomials

[Polynomials.jl](https://github.com/JuliaMath/Polynomials.jl) provides basic arithmetic, integration, differentiation, evaluation, and root finding over dense univariate polynomials.

[SpecialPolynomials.jl](https://github.com/jverzani/SpecialPolynomials.jl) provides many more classic orthogonal polynomials, the Lagrange and Newton interpolating polynomials, and the Bernstein polynomials.

## Legendre polynomials

There are packages like [PolynomialBases.jl](https://github.com/ranocha/PolynomialBases.jl).

For example, to evaluate the `p` order Legendre polynomial at `x`,

```julia:./1_3_legendre.jl
using PolynomialBases
x = 0.0
p = 1
@show legendre(x,p)
```

outputs

\output{./1_3_legendre.jl}

## Lagrange polynomials

To evaluate the value of Lagrange basis function on [-1,1]:

```julia:./lagrange.jl
"Computes values of pth Lagrange basis function at x between [-1,1]."
function lagrange(p::Integer, x)

   @assert p ≥ 0

   n = p + 1
   ϕ = ones(eltype(x),length(x),n)
   if n == 1
      return phi
   end

   InterPts = range(-1, 1, length=n)

   for i = 1:n
      for j = 1:n
         if j != i
            ϕ[i] *= (x - InterPts[j]) / (InterPts[i] - InterPts[j])
         end
      end
   end
   return ϕ
end

@show lagrange(1, 0.0)
```

gives
\output{./lagrange.jl}


A even easier approach would be taking advantage of the [Polynomials.jl](https://github.com/JuliaMath/Polynomials.jl) package and return the coefficients of the polynomials:

```julia:./lagrangeBasis.jl
using Polynomials

function lagrangeBasis(p::Integer)
   @assert p ≥ 0

   p == 0 && return [one(Polynomial)]

   roots = range(1, -1, length=p+1)
   ϕ = fill(one(Polynomial),p+1)
   ψ = Vector{Polynomial}(undef,p+1)
   for i = 1:p+1
      for j = 1:p+1
         if i != j
            temp = -1.0 / (roots[i]-roots[j])
            ψ[j] = Polynomial([roots[j]*temp, temp])
            ϕ[i] *= ψ[j]
         end
      end
   end
   return ϕ
end

@show lagrangeBasis(1)
@show lagrangeBasis(1)[1](1.0) # evaluate the first basis at 1.0
```

outputs

\output{./lagrangeBasis.jl}


## Barycentric polynomials

Another version of Lagrange polynomials, but more stable.
One implementation can be found at [BarycentricInterpolation.jl](https://github.com/dawbarton/BarycentricInterpolation.jl)

# Time Marching

Many functionalities, especially for ODEs, can be found at [DifferentialEquations.jl](https://docs.sciml.ai/stable/).

# Splines

中文对应样条插值。There are package like [Dierckx.jl](https://github.com/kbarbary/Dierckx.jl), [Interpolations.jl](https://github.com/JuliaMath/Interpolations.jl), and [GridInterpolations.jl](https://github.com/sisl/GridInterpolations.jl) for this purpose.

# Discretization Approaches