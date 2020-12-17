# Nelder Mead Simplex

Nelder Mead Simplex is a classic heuristic search method for minimizing an objective function in _n_-dimensional space.  While it is often used for unconstrained minimization, it can handle constrained minimization with the addition of a penalty function. This is not considered in the current implementation.  While the method can be used with discontinuous functions, it can also converge to non-stationary points.  It is also not guaranteed to find a global minimum; the method can become 'locked' in the area of a local minimum.

This implementation of Nelder Mead is optimized for small-dimensional use cases in which the cost of objective function values is large compared to the cost of a single (non-shrink) iteration of the algorithm.  Some suggestions for performance optimizations for other use cases are provided in the internal documentation.

Author:  Jim Armstrong - [The Algorithmist]

@algorithmist

theAlgorithmist [at] gmail [dot] com

Typescript: 3.8.3

Jest: 25.2.1

Version: 1.0


## Installation

Installation involves all the usual suspects

  - npm installed globally
  - Clone the repository
  - npm install
  - get coffee (this is the most important step)


### Building and running the tests

1. npm t (it really should not be this easy, but it is)

2. Standalone compilation only (npm build)

Specs (_nm.spec.ts_) reside in the ___tests___ folder.


### Usage

Advocates of functional programming should like this distribution as the Nelder Mean algorithm is implemented with a single function.  This function, however, requires some additional functions (and constants), all of which are exported from the _/src/nelderMead.ts_ file.  Simply import _nelderMead_ from wherever you place the file, i.e.,

```
import { nelderMead  } from "../src/nelderMead";
```

There is a zero-tolerance parameter, _NM_EPSILON_ that is defined in this file.  In a future release, it is likely to be defined adaptively based on problem size and its interpretation will change based on whether a domain- or function-value specific convergence test is used as a termination criteria.

The function signature is complex, but many of the arguments default to values that are likely to remain unchanged, so only a few arguments are actually used in practice.

```
export function nelderMead(
  initial: Array<number>,
  objective: (x: Array<number>, params?: object) => number,
  params: object = null,
  maxIterations: number = 0,
  unitLength: boolean = true,
  scale: number  = 1.0,
  reflect: number = 1.0,
  expand: number = 2.0,
  contract: number = 0.5,
  shrink: number = 0.5
): Array<number>
```

Here is a brief discussion of each argument.

_initial_ : This is the initial guess for a solution.  Nelder Mead is somewhat sensitive (in interation count) to the quality of the initial guess.  In some respects, it's like Newton's Method - the closer you start to a root, the quicker you get to a root :)

_object_ : This is the objective function to minimize that that takes an array of numerical values and returns a single number that is the objective-function value.  The number of elements in the array is the same as the dimensionality of the problem.  Optimizing a function of a single variable requires an input array with one number.  Optimizing a function of two independent variables, i.e. _z = f(x,y)_ requires an array of two numbers.

_params_: An optional array of parameters that is used as a well-defined scope in which parameters relative to the objective function may be obtained.  For example, consider the Rosenbrock function with parameters _a_ and _b_ .  See [https://en.wikipedia.org/wiki/Rosenbrock_function](https://en.wikipedia.org/wiki/Rosenbrock_function) for more information.  The parameters, _a_ and _b_ are passed in an object,

```
const rosenbrock: (x: Array<number>, params?: object) => number = (x: Array<number>, params?: object) => {
  const a: number  = params?.['a'] !== undefined ? +params['a'] : 1;
  const b: number  = params?.['b'] !== undefined ? +params['b'] : 100;
  let axsq: number = a - x[0];
  axsq            *= axsq;

  let bysq: number = x[1] - x[0]*x[0];
  bysq            *= bysq;
  bysq            *= b;

  return axsq + bysq;
};
```

_maxIterations_ : May be used to override the internal upper limit set on the number of Nelder Mead iterations.

_unitLength_ : Set to _true_ (the default) to create an initial simplex with sides of unit length.  If _false_, the initial simplex is derived by perturbing the components of the initial guess along each of the basis-vector axes.  The unit-side initial simplex often leads to a reduced iteration count.

_scale_ : An optional scale factor in the event a larger initial simplex is useful. This value defaults to 1.0 and is only referenced if _unitLength_ is _true_.

_reflect_ : Reflect coefficient (should be greater than zero and defaults to 1.0).

_expand_ : Expansion coefficient (should be greater than zero and defaults to 2.0).  For well-behaved objectives, an aggressive expansion coefficient can be beneficial, but the risk is over-expanding and having to compensate with a number of contractions.

_contract_ : Contract coefficient in (0, 0.5] - defaults to 0.5.

_shrink_ : Shrink coefficient in (0, 1) - defaults to 0.5.

Here is an example call for a one-dimensional function that is limited to no more than ten iterations.  The starting point is _x = -5_.

```
const f1: (x: Array<number>, params?: object) => number = (x: Array<number>) => {
  return Math.log( 1 + Math.pow(Math.abs(x[0]), 2 + Math.sin(x[0])) );
};

.
.
.

const solution: Array<number> = nelderMead([-5], f1, null, 10);
```

and here is an example call for the Rosenbrock function above that shows how to pass optional parameters.  This version runs until either a function-value convergence metric is passed or the iteration limit is reached.  There are known issues with many of the popular domain-convergence (i.e. size of simplex) tests, so the function-value metric is used but only every other iteration for performance.  The starting point is the two-dimensional point, (-1.2, 1).

```
 const solution: Array<number> = nelderMead([-1.2, 1], rosenbrock, {a: 1, b: 100});
```


Refer to the remainder of the specs for more usage examples.


### References

(1) D. J. Wilde and C. S. Beightler, Foundations of Optimization. Englewood Cliffs, N.J., Prentice-Hall, 1967

(2) F. Gao, L. Han, Implementing the Nelder Mead simplex algorithm with adaptive parameters, Computational

Optimization Applications, DOI 10.0007/s10589-010-9329-3

(3) Singer, Saša and Singer, Sanja (2004), “Efficient Implementation of the Nelder-Mead Search Algorithm”, Appl. Numer.
  Anal. Comput. Math. 1, No. 3, pp. 524–534.

License
----

Apache 2.0

**Free Software? Yeah, Homey plays that**

[//]: # (kudos http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

[The Algorithmist]: <https://www.linkedin.com/in/jimarmstrong/>

