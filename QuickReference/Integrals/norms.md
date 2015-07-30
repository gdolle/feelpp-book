# Norms

## $$L^2$$ norm


Let $$f \in L^2(\Omega)$$ you can evaluate the $$L^2$$ norm using the normL2() function:
<br><center>$$
  \begin{aligned}
\parallel f\parallel_{L^2(\Omega)}=\sqrt{\int_\Omega |f|^2}
  \end{aligned}
$$</center><br>

### Interface

```cpp
  normL2( _range, _expr, _quad, _geomap );
```

or squared norm:

```cpp
  normL2Squared( _range, _expr, _quad, _geomap );
```

Required parameters:
* `_range` = domain of integration
* `_expr`  = mesurable function

Optional parameters:
* `_quad`  = quadrature to use. Default = `_Q<integer>()`
* `_geomap`  = type of geometric mapping. Default = `GEOMAP_OPT`

### Example
From `doc/manual/laplacian/laplacian.cpp`
```cpp
  double L2error =normL2( _range=elements( mesh ),
                          _expr=( idv( u )-g ) );
```

From `doc/manual/stokes/stokes.cpp`
!CODEFILE "code/stokes.cpp" norml2

##  $$H^1$$ norm

In the same idea, you can evaluate the H1 norm or semi norm, for any
function $$f \in H^1(\Omega)$$:

$$\begin{aligned}
 \parallel f \parallel_{H^1(\Omega)}&=\sqrt{\int_\Omega |f|^2+|\nabla f|^2}\\
&=\sqrt{\int_\Omega |f|^2+\nabla f * \nabla f^T}\\
|f|_{H^1(\Omega)}&=\sqrt{\int_\Omega |\nabla f|^2}
\end{aligned}$$

where $$*$$ is the scalar product $$\cdot$$ when $$f$$ is a scalar
field and the frobenius scalar product $$:$$ when $$f$$ is a vector
field

### Interface
```cpp
  normH1( _range, _expr, _grad_expr, _quad, _geomap );
```
or semi norm:
```cpp
  normSemiH1( _range, _grad_expr, _quad, _geomap );
```

Required parameters:
* `_range` = domain of integration
* `_expr` = mesurable function
* `_grad_expr` = gradient of function (Row vector!)

Optional parameters:
* `_quad` = quadrature to use. Default = `_Q<integer>()`
* `_geomap` = type of geometric mapping. Default = `GEOMAP_OPT`

normH1() returns a float containing the $$H^1$$ norm.


### Example
With expression:
```cpp
  auto g = sin(2*pi*Px())*cos(2*pi*Py());
  auto gradg = 2*pi*cos(2* pi*Px())*cos(2*pi*Py())*oneX()
            -2*pi*sin(2*pi*Px())*sin(2*pi*Py())*oneY();
// There gradg is a column vector!
// Use trans() to get a row vector
  double normH1_g = normH1( _range=elements(mesh),
                     _expr=g,
                   _grad_expr=trans(gradg) );
```
With test or trial function `u`
```cpp
  double errorH1 = normH1( _range=elements(mesh),
                    _expr=(u-g),
                  _grad_expr=(gradv(u)-trans(gradg)) );
```



## Linfinity norm {#normLinf}

Let $$f$$ a bounded function on domain $$\Omega$$. You can evaluate the infinity norm using the normLinf() function:

$$\parallel f \parallel_\infty=\sup_\Omega(|f|)$$

### Interface
```cpp
  normLinf( _range, _expr, _pset, _geomap );
```

Required parameters:
* `_range` = domain of integration
* `_expr` = mesurable function
* `_pset` = set of points (e.g. quadrature points)

Optional parameters:
* `_geomap` = type of geometric mapping. Default = `GEOMAP_OPT`


The normLinf() function returns not only the maximum of the function over a sampling of each element thanks to the `_pset` argument but also the coordinates of the point where the function is maximum. The returned data structure provides the following interface
* `value()`: return the maximum value
* `operator()()`: synonym to `value()`
* `arg()`: coordinates of the point where the function is maximum

### Example

```cpp
  auto uMax = normLinf( _range=elements(mesh),
          _expr=idv(u),
          _pset=_Q<5>() );
  std::cout << "maximum value : " << uMax.value() << std::endl
            <<  "         arg : " << uMax.arg() << std::endl;
```