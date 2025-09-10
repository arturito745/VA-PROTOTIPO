# VA-PROTOTIPO

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
