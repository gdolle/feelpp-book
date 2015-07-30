# Integrals {#integrals}

Feel++ provide the integrate() function to define integral expressions which can be used to compute integrals, define linear and bi-linear forms.

**Interface***
```
  integrate( _range, _expr, _quad, _geomap );
```
please notice that the order of the parameter is not important, these are `boost` parameters, so you can enter them in the order you want. <br>
To make it clear, there are two required parameters and 2 optional and they of course can be entered in any order
provided you give the parameter name. If you don't provide the parameter name (that is to say `_range` = or the others) they must be entered in the order they are described
below.

Required parameters:
* `_range`  = domain of integration
* `_expr`  = integrand expression

Optional parameters:
* `_quad`  = quadrature to use instead of the default one, wich means `_Q<integer>()` where the integer is the polynomial order to integrate exactely
* `_geomap`  = type of geometric mapping to use, that is to say:

|Feel Parameter|Description|
|---|---|
| ```GEOMAP_HO``` | High order approximation (same of the mesh) |
| ```GEOMAP_OPT``` | Optimal approximation:<br> high order on boundary elements<br> order 1 in the interior |
| ``` GEOMAP_01``` | Order 1 approximation (same of the mesh) |

*Example*
From `doc/manual/tutorial/dar.cpp`
```cpp
  form1( ... ) = integrate( _range = elements( mesh ),
                            _expr = f*id( v ) );
```

From `doc/manual/tutorial/myintegrals.cpp`
```cpp
  // compute integral f on boundary
  double intf_3 = integrate( _range = boundaryfaces( mesh ),
                             _expr = f );
```

From `doc/manual/advection/advection.cpp`
```cpp
  form2( _test = Xh, _trial = Xh, _matrix = D ) +=
    integrate( _range = internalfaces( mesh ),
               _quad = _Q<2*Order>(),
               _expr = ( averaget( trans( beta )*idt( u ) ) * jump( id( v ) ) )
                     + penalisation*beta_abs*( trans( jumpt( trans( idt( u ) ) ) )*jump( trans( id( v ) ) ) ),
               _geomap = geomap );
```

From `doc/manual/laplacian/laplacian.cpp`
```cpp
 auto l = form1( _test=Xh, _vector=F );
 l = integrate( _range = elements( mesh ),
                _expr=f*id( v ) ) +
     integrate( _range = markedfaces( mesh, "Neumann" ),
                _expr = nu*gradg*vf::N()*id( v ) );
```


## Computing my first Integrals
This part explains how to integrate on a mesh with Feel++ (source `doc/manual/tutorial/myintegrals.cpp` ).

Let's consider the domain $$\Omega=[0,1]^d$$ and associated meshes.<br>
Here, we want to integrate the following function
<br><center>$$
\begin{aligned}
f(x,y,z) = x^2 + y^2 + z^2
\end{aligned}
$$</center><br>
on the whole domain $$\Omega$$ and on part of the boundary $$\Omega$$.

There is the appropriate code:
```cpp
int
main( int argc, char** argv )
{
    // Initialize Feel++ Environment
    Environment env( _argc=argc, _argv=argv,
                     _desc=feel_options(),
                     _about=about( _name="myintegrals" ,
                                   _author="Feel++ Consortium",
                                   _email="feelpp-devel@feelpp.org" ) );

    // create the mesh (specify the dimension of geometric entity)
    auto mesh = unitHypercube<3>();

    // our function to integrate
    auto f = Px()*Px() + Py()*Py() + Pz()*Pz();

    // compute integral of f (global contribution)
    double intf_1 = integrate( _range = elements( mesh ),
                               _expr = f ).evaluate()( 0,0 );

    // compute integral of f (local contribution)
    double intf_2 = integrate( _range = elements( mesh ),
                               _expr = f ).evaluate(false)( 0,0 );

    // compute integral f on boundary
    double intf_3 = integrate( _range = boundaryfaces( mesh ),
                               _expr = f ).evaluate()( 0,0 );

    std::cout << "int global ; local ; boundary" << std::endl
              << intf_1 << ";" << intf_2 << ";" << intf_3 << std::endl;
}
```