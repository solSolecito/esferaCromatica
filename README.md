# esferaCromatica

Muchas personas que conocen algo de teoria del color pueden estar de acuerdo conmigo en que circulo cromático se queda corto a la hora de representar los colores que como seres humanos podemos percibir. 

A la hora de trabajar con medios digitales, para representar el color solemos utilizar 3 parametros, ya sea por ejemplo **r, g, b** o **h, s, l**. Es por esto que una representación en 2 dimensiones hace que tengamos que elegir solamente dos variables para representarlas, ignorando muchas veces la luminosidad a la hora de mostrar un circulo cromático. 

Es por esto que la representación en 3 dimensiones de los colores no es algo nuevo, si entendemos los valores r,g y b como coordenadas obtenemos un cubo cromático.

![Representaciones RGB](https://upload.wikimedia.org/wikipedia/commons/d/d6/RGB_color_cube.svg)

Debido a la naturaleza de los valores del Matiz la representación de sistemas como el HSL o el HSV pasa a estar en coordenadas polares, con formas como esferas, cónicas, o cilindros.

![Representaciones HSL](https://upload.wikimedia.org/wikipedia/commons/1/13/Color_solid_comparison_hsl_hsv_rgb_cone_sphere_cube_cylinder.png)

El problema con el cilindro tanto para HSL como para HSV, es que existen multiples puntos con el mismo color, como es el caso del color negro

## Algoritmo

**Input** Dos colores de referencia
**Output** Una paleta de color circular que contiene los dos colores dados

### 0. Convertir los colores a HSL 

La conversión de color de rgb a hsl se realiza de la siguiente forma:

### 1. Convertir los colores HSL a puntos x,y,z

Teniendo una esfera cuya superficie está dada por la formula 
$$x^2+y^2+z^2=1$$

$$z = 2L - 1$$ 

Por esto cuando L = 0; z = -1, cuando L = 1; z = 1 y cuando L = 0.5, z = 0

$$( \frac{y} {x} ) = tan(H)$$

$$y = x tan(H)$$

$$r_{max} = \sqrt {1-z^2}$$

Cuando S = 0; r = 0, cuando S = 1; r = r_max

$$x^2 + y^2 = S^2 (1-z^2)$$

$$x^2 (1 + tan^2(H)) =  S^2 (1-z^2) $$

$$x =\sqrt {\frac {S^2 (1-z^2)} {(1 + tan^2(H))}}$$

$$x = S \sqrt {\frac {(1-z^2)} {(1 + tan^2(H))}}$$

**En Coordenadas esfericas**

$$ \theta = H $$

$$ r = \sqrt {  S^2 (1-z^2) + z^2} $$

$$ r = \sqrt {  S^2 (4L-4L^2) + (2L-1)^2} $$

$$ \phi = tan^{-1} ( \frac {(2L-1)} {\sqrt { S^2 (4L-4L^2)} } )$$

$$ \phi = tan^{-1} ( \frac { 2L-1 } { 2S \sqrt {L - L^2} } )$$

### 2. Revisar si los puntos son coolineales con el origen

**En Coordenadas esfericas**

Dos puntos serian coolineales con el origen en coordenadas esfericas si 

$\theta_1 = \theta_2$ y $\phi_1 = \phi_2$ 

o si

$|\theta_1 - \theta_2| = \pi$ y $\phi_1 = -\phi_2$ 

**SI SON COLINEALES** 

Hay que pedir un tercer punto 

### 3. Encontrar la ecuación del plano que contiene los puntos y pasa por el centro


```
// 3. Multiplicar vectores
  const normal = vector.cruz(points[0], points[1]);
  // la ecuación del plano que se mostrará es [n] . [x,y,z] = 0
  console.log("n", normal);
  // x = -n1 y /n0 - n2 z / n0
  // x = 0 cuando z = - n1 y/ n2
  // z = 0 cuando y = - n0 x / n1
  const omega = ( ( Math.atan( normal[1] / normal[0]) ) + Math.PI * (( normal[2] < 0 ? 1 : 0) + 1/2 + ( normal[0] < 0 ? 1 : 2) )) % ( 2 * Math.PI) ;
  const incl =
    ((Math.PI/2) +
      Math.atan(-normal[2] / normal[1])) %
    (Math.PI/2);
  const mi = Math.atan( Math.abs ( normal[2] /  ( normal[0] * normal[1] / Math.sqrt(Math.pow(normal[0], 2) + Math.pow(normal[1], 2)) )))
```

Si  un plano en coordenadas rectangulares viene dado de la forma 

$$ n_1 x + n_2 y + n_3 z = 0 $$ 

Donde 

$$ n = \[ y_1 z_2 - y_2 z_1,  z_1 x_2 - z_2 x_1,  x_1 y_2 - x_2 y_1 \]$$

En coordenadas polares seria

$$ n_1 r cos( \theta ) cos( \phi ) + n_2 r sin( \theta ) cos( \phi ) + n_3 r sin( \phi ) = 0 $$

$$ n = \[ 
2S_1 tan(H_1) \sqrt {\frac {(L_1 - L_1^2)} {(1 + tan^2(H_1))}} (2L_2 - 1) - 2S_2 tan(H_1) \sqrt {\frac {(L_2 - L_2^2)} {(1 + tan^2(H_2))}} (2L_1 - 1),  
2S_2 \sqrt {\frac {(L_2 - L_2^2)} {(1 + tan^2(H_2))}}  (2L_1 - 1) - 2S_1 \sqrt {\frac {(L_1 - L_1^2)} {(1 + tan^2(H_1))}}  (2L_2 - 1), 
4 S_1 S_2 \sqrt {\frac {(L_1 - L_1^2) (L_2 - L_2^2)} {(1 + tan^2(H_1))(1 + tan^2(H_2))}} (tan(H_2)-tan(H_1)) \]$$

$$ n_1 cos( \theta ) cos( \phi ) + n_2 sin( \theta ) cos( \phi ) + n_3 sin( \phi ) = 0 $$

$$ n_1 cos( \theta ) cos( \phi ) + n_2 sin( \theta ) cos( \phi ) = - n_3 sin( \phi )  $$

$$ cos( \phi ) ( n_1 cos( \theta ) + n_2 sin( \theta ) ) = - n_3 sin( \phi )  $$

$$ \frac {- n_3 sin( \phi )} {cos( \phi ) ( n_1 cos( \theta ) + n_2 sin( \theta ) )} = 1 $$

$$ tan( \phi ) \frac {- n_3 } { n_1 cos( \theta ) + n_2 sin( \theta ) } = 1 $$

$$ tan( \phi ) =  - \frac { n_1 cos( \theta ) + n_2 sin( \theta ) } { n_3 }  $$
