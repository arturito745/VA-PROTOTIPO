# VA-PROTOTIPO


# Bitácora 


## Visión Predadora

El proyecto busca que la cámara no muestre el cuerpo como tal, sino su **energía en forma de visión térmica**, inspirada en el estilo de *Predator*. Los movimientos suaves se pintan en verdes y azules, mientras que los gestos intensos explotan en rojos y amarillos, como si el cuerpo fuese un radar de calor.

La idea es transmitir la sensación de **ser visto como presencia y no como rostro**: cada gesto deja un rastro, cada pausa se siente como calma. No es un espejo realista, sino una **traducción abstracta del movimiento en colores vivos y efímeros**.
[Ver referencia: Estilo “visión térmica Predator”](https://www.youtube.com/watch?v=OW1gGDbO_1U)




## Referentes

Para darle forma a la propuesta revisamos varios referentes. De los **mapas de calor** en seguridad tomamos la lógica de traducir el movimiento en intensidades visibles. De proyectos con **Kinect y visuales en vivo** vimos cómo la interacción corporal puede ser la base de un espectáculo generativo. También nos inspiramos en artistas como **Memo Akten, Rafael Lozano-Hemmer y Golan Levin**, que trabajan con la idea de convertir la presencia humana en experiencias digitales abstractas, donde el cuerpo se traduce en luz, energía y trazos. Esa mezcla de inspiración nos llevó a pensar que lo nuestro debía ser un sistema sencillo pero expresivo, donde cada movimiento deje su huella en tiempo real.

---

###  Referentes artísticos y conceptuales

1. **Rafael Lozano-Hemmer – *Body Movies***

   * Proyecciones a gran escala donde las siluetas de los transeúntes se transforman en imágenes.

2. **Daniel Rozin – *Digital Mirrors***

   * Espejos interactivos que reconstruyen el cuerpo con materiales o píxeles.

3. **Camille Utterback – *Text Rain***

   * Letras que caen como lluvia y se detienen en las siluetas de los cuerpos de los espectadores.

4. **Golan Levin – *Messa di Voce***

   * Voz y movimiento corporal generan visualizaciones en tiempo real.

5. **Daito Manabe**

   * Artista japonés que combina cuerpo, movimiento y visualizaciones digitales en performances.

---

###  Referentes técnicos

6. **Processing Motion Detection examples** (Daniel Shiffman – *Coding Train* en YouTube).

   * Tutoriales de comparación de frames.

7. **p5.js webcam + particles sketches**

   * Ejemplos donde la cámara controla sistemas de partículas y trazos.

8. **OpenFrameworks / OpenCV Motion Tracking**

   * Librerías que trabajan con detección de movimiento y siluetas


## Parte técnica

Para hacerlo posible trabajamos con **p5.js** y la cámara como insumo. La técnica parte de algo básico: comparar un frame con el anterior y detectar sus diferencias. Esas diferencias se convierten en puntos, luego en colores, y más adelante en estelas. 

* Un **slider de sensibilidad** para calibrar el threshold en tiempo real.
* El uso de **HSB** para crear un mapa de calor (azules en movimientos suaves, rojos en los intensos).
* Un **buffer gráfico (`createGraphics`)** para que los gestos no desaparezcan al instante, sino que queden como un eco o rastro.

La planificación se pensó en pasos muy claros: primero probar la detección en blanco y negro, luego añadir control, después trabajar la paleta de color y finalmente integrar el efecto de estela.

---

¿Quieres que ahora siga con la **segunda parte de la bitácora** (primeras pruebas en blanco y negro, problemas con el ruido y cómo lo resolviste con el slider)?

# El proceso

Cuando empecé con este proyecto lo primero fue buscar referentes. Vi algunos ejemplos de mapas de calor en seguridad, proyectos hechos con Kinect y también visuales en vivo hechos en Processing/p5.js. Eso me inspiró a pensar el movimiento no solo como detección técnica, sino como algo que deja una huella visual como las estelas que me gustan mucho. Esa idea de rastro fue la que guió casi todo el diseño.

---

## Primeras pruebas

El código comenzó súper simple: solo comparar un frame con el anterior y pintar en blanco los píxeles que habían cambiado.

```
let video;
let prevFrame;
let threshold = 25;

function setup() {
  createCanvas(640, 480);
  pixelDensity(1);

  video = createCapture(VIDEO);
  video.size(width, height);
  video.hide();

  prevFrame = createImage(width, height);
}

function draw() {
  background(0);

  video.loadPixels();
  prevFrame.loadPixels();

  if (video.pixels.length > 0 && prevFrame.pixels.length > 0) {
    for (let x = 0; x < width; x++) {
      for (let y = 0; y < height; y++) {
        let index = (x + y * width) * 4;

        let r = video.pixels[index + 0];
        let g = video.pixels[index + 1];
        let b = video.pixels[index + 2];

        let rPrev = prevFrame.pixels[index + 0];
        let gPrev = prevFrame.pixels[index + 1];
        let bPrev = prevFrame.pixels[index + 2];

        let diff = dist(r, g, b, rPrev, gPrev, bPrev);

        if (diff > threshold) {
          stroke(255);
          point(x, y);
        }
      }
    }
  }

  prevFrame.copy(video, 0, 0, width, height, 0, 0, width, height);
}

```
<img width="817" height="655" alt="image" src="https://github.com/user-attachments/assets/3c080c3b-5ad8-4a2a-b79a-b78e79f3d88e" />

Esto funcionaba, pero tenía un problema: cualquier mínimo ruido o movimiento de luz activaba demasiados píxeles y todo se volvía caótico.

---

## Sumando controles

La primera solución fue agregar un **threshold** para filtrar el ruido. Luego pensé que no era suficiente dejarlo fijo, porque dependía mucho de la luz o del entorno, así que puse un **slider** para poder calibrar la sensibilidad. Eso ya le dio más control y hacía que el sistema fuera usable en diferentes espacios.

```js
thresholdSlider = createSlider(0, 100, 25, 1);
```

---

## Buscandondo los colores 

El blanco y negro se sentía muy plano, así que probé con colores. Al principio usé RGB, pero no me convencía. Pasar a **HSB** me permitió mapear el movimiento como una escala de calor: azul cuando es leve, rojo cuando es intenso.

```js
let hueVal = lerp(200, 20, intensity);
let col = color(hueVal, 100, 100, 60);
```
```js
        let b = video.pixels[index + 2];

        let rPrev = prevFrame.pixels[index + 0];
        let gPrev = prevFrame.pixels[index + 1];
        let bPrev = prevFrame.pixels[index + 2];

        let diff = dist(r, g, b, rPrev, gPrev, bPrev);

        if (diff > threshold) {
          // Mapear la intensidad de la diferencia a [0,1]
          let intensity = map(diff, 0, 150, 0, 1);
          intensity = constrain(intensity, 0, 1);

          // Escala de calor (azul → rojo)
          let hueVal = lerp(200, 20, intensity);
          let col = color(hueVal, 100, 100, 80);

          stroke(col);
          strokeWeight(4);
          point(x, y);
        }
      }
    }
  }

  
  prevFrame.copy(video, 0, 0, width, height, 0, 0, width, height);

  
  noStroke();
  fill(255);
  textSize(14);
  text("Sensibilidad: " + threshold, 220, height + 25);
}

```
<img width="818" height="647" alt="image" src="https://github.com/user-attachments/assets/3b87482b-3dde-4cde-b5ef-85d7392513d5" />
<img width="825" height="537" alt="image" src="https://github.com/user-attachments/assets/800600c4-2ae9-426b-8fc1-5801529abd22" />


Esa decisión estética cambió por completo el resultado: dejó de ser un test técnico y empezó a parecer una visual generativa.

---

## El dejar rastro o estela del movimiento 

Otro paso clave fue dar un efecto de eco. Usé un `createGraphics()` extra con un rectángulo negro semitransparente para ir borrando lentamente los frames anteriores. Eso hizo que el movimiento quedara como una estela en el aire.

```js
trailImg.fill(0, 0, 0, 5);
trailImg.rect(0, 0, width, height);
```
```js
       let video;
let prevFrame;
let threshold = 25;
let thresholdSlider;
let trailImg; // buffer para el rastro

function setup() {
  createCanvas(640, 480);
  pixelDensity(1);

  video = createCapture(VIDEO);
  video.size(width, height);
  video.hide();

  prevFrame = createImage(width, height);
  trailImg = createGraphics(width, height); // lienzo aparte
  trailImg.clear();

  // Slider para controlar el threshold
  thresholdSlider = createSlider(0, 100, 25, 1);
  thresholdSlider.position(10, height + 10);
  thresholdSlider.style('width', '200px');

  // Usamos modo HSB para colores tipo mapa de calor
  colorMode(HSB, 360, 100, 100, 100);
  trailImg.colorMode(HSB, 360, 100, 100, 100); // mismo modo para el buffer
}

function draw() {
  background(0);

  threshold = thresholdSlider.value();

  video.loadPixels();
  prevFrame.loadPixels();

  if (video.pixels.length > 0 && prevFrame.pixels.length > 0) {
    for (let x = 0; x < width; x += 6) {
      for (let y = 0; y < height; y += 6) {
        let index = (x + y * width) * 4;

        let r = video.pixels[index + 0];
        let g = video.pixels[index + 1];
        let b = video.pixels[index + 2];

        let rPrev = prevFrame.pixels[index + 0];
        let gPrev = prevFrame.pixels[index + 1];
        let bPrev = prevFrame.pixels[index + 2];

        let diff = dist(r, g, b, rPrev, gPrev, bPrev);

        if (diff > threshold) {
          let intensity = map(diff, 0, 150, 0, 1);
          intensity = constrain(intensity, 0, 1);

          let hueVal = lerp(200, 20, intensity);
          let col = color(hueVal, 100, 100, 80);

          // Dibujar en el buffer trailImg
          trailImg.noStroke();
          trailImg.fill(col);
          trailImg.rect(x, y, 6, 6);
        }
      }
    }
  }

  // Aplicar desvanecimiento al rastro
  trailImg.fill(0, 0, 0, 5);
  trailImg.noStroke();
  trailImg.rect(0, 0, width, height);

  // Mostrar rastro en pantalla
  image(trailImg, 0, 0);

  // Guardar el frame actual
  prevFrame.copy(video, 0, 0, width, height, 0, 0, width, height);

  // Texto de control
  noStroke();
  fill(255);
  textSize(14);
  text("Sensibilidad: " + threshold, 220, height + 25);
}


```
<img width="818" height="651" alt="image" src="https://github.com/user-attachments/assets/314e893e-a922-4c82-a0ce-0b32458c485e" />

Al principio lo configuré con más opacidad y borraba demasiado rápido. Después lo bajé y logré un balance donde las huellas permanecen un poco más, pero igual desaparecen con el tiempo.

Listo, aquí te lo dejo en un tono más relax, tipo bitácora, con el título y ejemplos de código para que quede más natural.


## Desafíos técnicos generales

### 1. Ruido en la cámara

Al inicio el sistema detectaba movimiento aunque yo estuviera quieto. La cámara siempre tiene un poco de ruido o cambios de luz y eso hacía que se activaran demasiados píxeles. El resultado era una pantalla llena de puntos aleatorios.

La primera solución fue usar un **threshold fijo**:

```js
if (diff > 25) {
  stroke(255);
  point(x, y);
}
```

Pero eso no era suficiente, porque cambiaba mucho según el espacio donde estaba. Entonces lo mejor fue poner un **slider** para calibrar en tiempo real:

```js
thresholdSlider = createSlider(0, 100, 25, 1);
```


### 2. Colores planos que no transmitían nada

En un inicio todo era blanco y negro. Luego probé con colores en RGB, pero se veían planos, sin gracia, más como un test técnico que como algo artístico.

Lo que cambió todo fue usar **HSB** y mapear la intensidad a una escala de calor:

```js
let intensity = map(diff, 0, 150, 0, 1);
let hueVal = lerp(200, 20, intensity);
let col = color(hueVal, 100, 100, 60);
```

Con esto ya se sentía más como visión térmica: azules en movimientos suaves y rojos intensos cuando había mucha actividad.

### 3. El rastro que se borraba muy rápido

Otro problema fue que los dibujos desaparecían enseguida, no quedaba esa sensación de estela que buscaba. Al principio dibujaba directo en el canvas principal y todo se reiniciaba en cada frame.

La solución fue crear un **buffer extra** (`trailImg`) y aplicar un rectángulo negro semitransparente en cada actualización:

```js
trailImg.fill(0, 0, 0, 5);
trailImg.noStroke();
trailImg.rect(0, 0, width, height);
```

Así el movimiento no se borra de golpe, sino que queda flotando un poco más en la pantalla antes de desaparecer.

## Pruebas con usuarios

Probé el sistema con amigos. Si dejábamos el threshold muy bajo, cualquier micro movimiento se veía, incluso el parpadeo de la cámara. Si lo subíamos, solo movimientos grandes aparecían. Esa calibración fue fundamental.

Lo interesante fue ver cómo la gente lo usaba distinto: unos hacían gestos suaves para dibujar estelas lentas, otros bailaban para llenar la pantalla de colores.


## Reflexión

El proyecto fue avanzando por capas:

* De un inicio súper crudo en blanco y negro.
* A un sistema calibrable y fluido.
* Después se sumó el color y el rastro.
* Y finalmente, las variaciones visuales según la energía del movimiento.

Al final no se siente como un programa técnico, sino como un espejo que traduce la intensidad del cuerpo en una experiencia visual.
[![Demo en YouTube](https://img.youtube.com/vi/mXAYCqA5CLo/0.jpg)](https://youtu.be/mXAYCqA5CLo)
# Codigo final 
```
let video;
let prevFrame;
let trailImg;
let threshold = 25;
let thresholdSlider;

function setup() {
  createCanvas(640, 480);
  pixelDensity(1);

  // activamos la cámara
  video = createCapture(VIDEO);
  video.size(width, height);
  video.hide(); // escondemos la vista directa de la cámara

  // guardamos un frame anterior para comparar
  prevFrame = createImage(width, height);

  // lienzo extra para dibujar rastros
  trailImg = createGraphics(width, height);
  trailImg.clear();

  // slider para controlar qué tan sensible es a los movimientos
  thresholdSlider = createSlider(0, 100, threshold, 1);
  thresholdSlider.position(10, height + 10);
  thresholdSlider.style('width', '200px');

  // usamos HSB para que el color cambie como un mapa de calor
  colorMode(HSB, 360, 100, 100, 100);
}

function draw() {
  background(0, 0, 10); // fondo negro suave

  // actualizar el threshold con lo que diga el slider
  threshold = thresholdSlider.value();

  // cargamos pixeles actuales y los del frame anterior
  video.loadPixels();
  prevFrame.loadPixels();

  // si hay info en ambos frames, hacemos la comparación
  if (video.pixels.length > 0 && prevFrame.pixels.length > 0) {
    for (let x = 0; x < width; x += 6) {    // saltamos de 6 en 6 para no colapsar el pc
      for (let y = 0; y < height; y += 6) {
        let index = (x + y * width) * 4;

        // colores actuales
        let r = video.pixels[index + 0];
        let g = video.pixels[index + 1];
        let b = video.pixels[index + 2];

        // colores del frame anterior
        let rPrev = prevFrame.pixels[index + 0];
        let gPrev = prevFrame.pixels[index + 1];
        let bPrev = prevFrame.pixels[index + 2];

        // qué tanto cambió el pixel (distancia entre colores)
        let diff = dist(r, g, b, rPrev, gPrev, bPrev);

        // si el cambio pasa el threshold, dibujamos algo
        if (diff > threshold) {
          // normalizamos el cambio a un rango [0,1]
          let intensity = map(diff, 0, 150, 0, 1);
          intensity = constrain(intensity, 0, 1);

          // colores estilo mapa de calor: azul → rojo
          let hueVal = lerp(200, 20, intensity);
          let col = color(hueVal, 100, 100, 60);

          // dibujamos un cuadradito de color en el lienzo del rastro
          trailImg.noStroke();
          trailImg.fill(col);
          trailImg.rect(width - x, y, 6, 6); // espejo horizontal
        }
      }
    }
  }

  // este rect semi-transparente borra un poquito el lienzo
  // así se genera el efecto de eco/estela
  trailImg.fill(0, 0, 0, 5);
  trailImg.noStroke();
  trailImg.rect(0, 0, width, height);

  // mostramos el trail en pantalla
  image(trailImg, 0, 0);

  // guardamos el frame actual para la próxima comparación
  prevFrame.copy(video, 0, 0, width, height, 0, 0, width, height);

  // texto de control para ver el valor del slider
  noStroke();
  fill(255);
  textSize(14);
  text("Sensibilidad: " + threshold, 220, height + 25);
}
```
