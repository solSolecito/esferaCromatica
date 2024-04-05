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

### 1. Convertir los colores HSL a puntos $(r, \theta, \phi)$

$$ r = \sqrt {  S^2 (4L-4L^2) + (2L-1)^2} $$

$$ \theta = H $$

$$ \phi = tan^{-1} ( \frac { 2L-1 } { 2S \sqrt {L - L^2} } )$$

### 2. Revisar si los puntos son coolineales con el origen

**En Coordenadas esfericas**

Dos puntos serian coolineales con el origen en coordenadas esfericas si 

$\theta_1 = \theta_2$ y $\phi_1 = \phi_2$ 

o si

$|\theta_1 - \theta_2| = \pi$ y $\phi_1 = -\phi_2$ 

**SI SON COLINEALES** 

Hay que pedir un tercer punto, se borra el primero y se realiza nuevamente el procedimiento 

### 3. Encontrar la ecuación del plano que contiene los puntos y pasa por el centro

Si  un plano en coordenadas rectangulares viene dado de la forma 

$$ n_1 x + n_2 y + n_3 z = 0 $$ 

Donde 

$$ n = color_1 \times color_2 $$

$$ n_1 = \frac { sin( \theta_1 ) cos( \phi_2 ) - sin( \theta_2 ) cos( \phi_1 )  } { sin ( \theta_2 - \theta_1 ) }

$$ n_2 = \frac { cos( \theta_2 ) cos( \phi_1 ) - cos( \theta_1 ) cos( \phi_2 )  } { sin ( \theta_2 - \theta_1 ) }

$$ n_3 = 1 $$

Reemplazando x,y y z

$$ n_1 cos( \theta ) + n_2 sin( \theta ) + tan( \phi ) = 0  $$

## Memoria de Calculo

Teniendo una esfera cuya superficie está dada por la formula 

$$x^2+y^2+z^2=1$$

### Cambio de coordenadas

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

### Ecuación del plano

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

Dividiendo entre $n_3$

$$ n_1 = \frac { sin( \theta_1 ) cos( \phi_2 ) - sin( \theta_2 ) cos( \phi_1 ) } { sin ( \theta_2 - \theta_1 ) }

$$ n_2 = \frac { cos( \theta_2 ) cos( \phi_1 ) - cos( \theta_1 ) cos( \phi_2 ) } { sin ( \theta_2 - \theta_1 ) }


$$ n_1 cos( \theta ) cos( \phi ) + n_2 sin( \theta ) cos( \phi ) + n_3 sin( \phi ) = 0 $$

$$ n_1 cos( \theta ) cos( \phi ) + n_2 sin( \theta ) cos( \phi ) = - n_3 sin( \phi )  $$

$$ cos( \phi ) ( n_1 cos( \theta ) + n_2 sin( \theta ) ) = - n_3 sin( \phi )  $$

$$ \frac {- n_3 sin( \phi )} {cos( \phi ) ( n_1 cos( \theta ) + n_2 sin( \theta ) )} = 1 $$

$$ tan( \phi ) \frac {- n_3 } { n_1 cos( \theta ) + n_2 sin( \theta ) } = 1 $$

$$ tan( \phi ) =  - n_1 cos( \theta ) - n_2 sin( \theta ) $$

$$ sin ( \phi ) = - \frac { n_1 cos( \theta ) + n_2 sin( \theta ) } { sqrt { 1 + ( - n_1 cos( \theta ) - n_2 sin( \theta ) )^2 } }

$$ cos ( \phi ) = \frac { 1 } { sqrt { 1 + ( - n_1 cos( \theta ) - n_2 sin( \theta ) )^2 } }

Reescribiendo la raiz

$$ ( - n_1 cos( \theta ) - n_2 sin( \theta ) )^2 = {n_1}^2 cos^2( \theta ) + 2 n_1 n_2 cos( \theta ) sin( \theta ) + {n_2}^2 sin^2( \theta )  $$

$$ {n_1}^2 = \frac { sin^2( \theta_1 ) cos^2( \phi_2 ) - 2 sin( \theta_1 ) cos( \phi_2 )sin( \theta_2 ) cos( \phi_1 ) + sin^2( \theta_2 ) cos^2( \phi_1 ) } { sin^2 ( \theta_2 - \theta_1 ) } $$

$$ {n_2}^2 = \frac { cos^2( \theta_2 ) cos^2( \phi_1 ) - 2 cos( \theta_1 ) cos( \phi_2 )cos( \theta_2 ) cos( \phi_1 ) + cos^2( \theta_1 ) cos^2( \phi_2 ) } { sin^2 ( \theta_2 - \theta_1 ) } $$

$$ n_1 n_2 = \frac { sin( \theta_1 ) cos( \theta_2 ) cos( \phi_2 ) cos( \phi_1 ) - sin( \theta_1 ) cos( \theta_1 ) cos^2( \phi_2 ) - sin( \theta_2 ) cos( \theta_2 )  cos^2( \phi_1 ) + sin( \theta_2 ) cos( \theta_1 ) cos( \ phi_2 ) cos( \phi_1 )  } { sin^2 ( \theta_2 - \theta_1 ) } $$

$$ \frac { (sin( \theta_1 ) cos( \phi_2 ) - sin( \theta_2 ) cos( \phi_1 ))^2 cos^2( \theta ) + 2 (sin( \theta_1 ) cos( \phi_2 ) - sin( \theta_2 ) cos( \phi_1 )) ( cos( \theta_2 ) cos( \phi_1 ) - cos( \theta_1 ) cos( \phi_2 )) cos( \theta ) sin( \theta ) + ( cos( \theta_2 ) cos( \phi_1 ) - cos( \theta_1 ) cos( \phi_2 ))^2 sin^2( \theta ) } { sin^2( \theta_2 - \theta_1 ) }  $$ 

$$ \frac { (sin( \theta_1 ) cos( \phi_2 ) - sin( \theta_2 ) cos( \phi_1 ))^2 cos^2( \theta ) + 2 (sin( \theta_1 ) cos( \phi_2 ) - sin( \theta_2 ) cos( \phi_1 )) ( cos( \theta_2 ) cos( \phi_1 ) - cos( \theta_1 ) cos( \phi_2 )) cos( \theta ) sin( \theta ) + ( cos( \theta_2 ) cos( \phi_1 ) - cos( \theta_1 ) cos( \phi_2 ))^2 sin^2( \theta ) } { sin^2( \theta_2 - \theta_1 ) }  $$ 

$$ 1 + ( - n_1 cos( \theta ) - n_2 sin( \theta ) )^2 $$


