# IG-Shaders

## Shaders en prácticas anteriores
He aplicado el shader de doble textura en la práctica del modelo del Sistema Solar. Consiguiendo que en el planeta Tierra dependiendo de la luz, se muestre en la superficie de este la textura del planeta de día o la textura de la noche con las luces de las ciudades. Para que no fuese un corte completo he decidido no usar una estrcutura de if/else, y en cambio se ha usado un 

## Shaders de fragmentos
En esta parte se detalla el desarrollo de los shaders de patrones generativos ejecutables en The Book of Shaders.

### Efecto Caleidoscópico

```
uniform vec2 u_resolution; uniform float u_time;
void main(){
 vec2 st=gl_FragCoord.xy/u_resolution;
 vec2 c=st-0.5;
 float r=length(c)*2.;
 float a=atan(c.y,c.x);
 float v=sin(10.*r+u_time*0.8)+sin(6.*a+u_time);
 vec3 col=vec3(0.2,0.5,0.9)*(1.-smoothstep(0.4,1.0,r)) + 0.6*vec3(1.)*smoothstep(0.2,0.7, fract(v*0.5));
 gl_FragColor=vec4(col,1.0);
}
```
El primer uniform es para el tamaño de los píxeles en el canvas, el segundo para el tiempo en segundos.
vec2 st = gl_FragCoord.xy / u_resolution; 
vec2 c = st - 0.5; 
float r = length(c) * 2.;
float a = atan(c.y, c.x);

El shader toma primero las coordenadas del pixel en la pantalla y las convierte en un sistema más fácil de manejar: en vez de trabajar con píxeles, se normaliza el espacio para que el punto (0,0) quede en la esquina y luego se desplaza todo para que el centro de la imagen sea (0,0). Con este cambio, cada píxel tiene dos valores interesantes: cuánto se aleja del centro (la distancia radial) y en qué dirección se encuentra respecto de ese centro (el ángulo polar). Esto permite manipular la pantalla como si fuera un sistema circular: como si todas las coordenadas estuvieran colocadas alrededor de un punto central.

float v = sin(10. * r + u_time * 0.8) + sin(6. * a + u_time);
sin(10*r + u_time*0.8): una onda radial (anillos concéntricos) cuya frecuencia radial es 10. u_time*0.8 desplaza la fase en el tiempo (animación).
sin(6*a + u_time): una onda angular (varía según el ángulo); añade vetas u ondulaciones alrededor del círculo.

vec3 col = vec3(0.2,0.5,0.9) * (1. - smoothstep(0.4,1.0,r)) + 0.6 * vec3(1.) * smoothstep(0.2,0.7, fract(v*0.5));

Una vez que tenemos el ángulo a calculado con atan(c.y, c.x), sabemos exactamente “hacia dónde apunta” cada píxel. Ese valor crece cuando giramos alrededor del centro, así que si aplicamos funciones periódicas como sin(6*a) obtenemos ondas que se repiten alrededor del círculo, como los radios de una rueda o como un patrón que gira. De la misma manera, la distancia radial r nos dice qué tan lejos está el píxel del centro. Cuando aplicamos sin(10*r) a ese valor, obtenemos anillos concéntricos, porque la función seno oscila regularmente a medida que r aumenta. Por eso, una onda basada en r produce círculos; una onda basada en a produce líneas que giran alrededor del centro.

El shader usa dos ondas: una basada en la distancia al centro (crea anillos) y otra basada en el ángulo alrededor del centro (crea rayos). Al sumar estas dos ondas, el patrón resultante ya no es solo circular ni radial, sino una mezcla de ondulaciones y remolinos que cambian con el tiempo gracias a u_time, lo que da la sensación de movimiento orgánico.

Después, el color se construye como dos capas: una base azul que se difumina hacia los bordes usando smoothstep sobre la distancia radial, y encima una capa blanca que aparece solo en ciertas partes de las ondas. Para seleccionar esas zonas, el shader usa fract(v*0.5), que convierte la onda en bandas repetidas, y smoothstep para suavizarlas. Esta capa blanca agrega brillo y textura.

gl_FragColor=vec4(col,1.0);

Finalmente, ambas capas se combinan y se envían a la pantalla con gl_FragColor. La línea final no crea el efecto; el efecto nace de cómo las matemáticas polares (radio y ángulo) se convierten en ondas, y cómo esas ondas generan formas animadas y fluidas.

### Hélice Psicodélica
```
uniform vec2 u_resolution;uniform float u_time;
void main(){
 vec2 st=gl_FragCoord.xy/u_resolution-0.5;
 float a=atan(st.y,st.x)+u_time*0.3;
 float r=length(st);
 float v=sin(12.*r+a*6.);
 vec3 c=vec3(0.5+0.5*sin(a+v),0.4+0.6*v,0.6-0.5*sin(a));
 gl_FragColor=vec4(c,1.);
}
```


