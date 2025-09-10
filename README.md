# VA-PROTOTIPO
que mas me falta despues de hacer la bitacora y ya todo lo tecnico todo esto me van a evaluar # Guía para el Desarrollo del Proyecto de Medio Semestre

# Introducción

Este proyecto integra conceptos fundamentales de visión artificial con la creación de contenido visual interactivo. Utilizarás p5.js para desarrollar una experiencia donde la detección de movimiento se convierte en el elemento central de interacción con gráficos generativos. Este proyecto te permitirá explorar la intersección entre tecnología y arte, aplicando técnicas de visión artificial desarrolladas durante la primera mitad del semestre para crear una experiencia visual e interactiva.

# Componentes Clave

1. Detección de movimiento (background subtraction y frame difference)
2. Procesamiento de imágenes en tiempo real con p5.js
3. Generación de contenido visual interactivo
4. Análisis de intensidad y dirección de movimiento
5. Integración de visión artificial y diseño generativo

# Herramientas y Técnicas

- p5.js como plataforma principal de desarrollo
- Técnicas de detección de movimiento: background subtraction y frame difference
- Procesamiento de imágenes: thresholding, máscaras, análisis de píxeles
- Análisis de movimiento: velocidad, dirección, intensidad
- Programación creativa para la generación de contenido artístico

# Objetivo del Proyecto

Crear una experiencia interactiva en p5.js que utilice la detección de movimiento corporal como input para manipular y generar contenido visual artístico, aplicando técnicas de Visión Artificial y principios de diseño generativo.

## Estructura del Proyecto

El proyecto se desarrollará en **dos etapas** durante **dos semanas**:

### **Semana 1: Etapa de Diseño**

**Enfoque:** Ideación, investigación y planificación

**Actividades:**

1. **Ideación de la experiencia**
    - Definir el concepto artístico/interactivo
    - Seleccionar una de las modalidades de detección de movimiento (ver sección de Desafíos)
    - Establecer la narrativa o propuesta estética
2. **Búsqueda de referentes**
    - Investigar instalaciones interactivas similares
    - Analizar proyectos de arte digital y new media
    - Estudiar casos de uso de Visión Artificial en arte
    - 
3. **Diseño en papel**
    - Bocetos de la interfaz y experiencia visual
    - Diagramas de flujo de la interacción
    - Wireframes de la composición visual
    - Storyboard de la experiencia del usuario
4. **Planificación técnica**
    - Definir uso específico del cuerpo (gestos, movimientos, posiciones)
    - Determinar ubicación óptima de la cámara
    - Especificar área de detección y zona de interacción
    - Planear la lógica de procesamiento de movimiento

### **Semana 2: Etapa de Implementación**

**Enfoque:** Desarrollo técnico y refinamiento

**Actividades:**

1. **Implementación del sistema de detección**
    - Configuración de captura de video
    - Desarrollo de algoritmos de background subtraction/frame difference
    - Calibración de sensibilidad y thresholds
2. **Desarrollo del contenido generativo**
    - Programación de elementos visuales reactivos
    - Implementación de la lógica de interacción
    - Integración entre detección y generación visual
3. **Optimización y pulimiento**
    - Ajuste de rendimiento en tiempo real
    - Refinamiento de la experiencia de usuario
    - Documentación del código y proceso

## Ideas de Interacción con Detección de Movimiento

### **Opción 1: Detector de Velocidad**

Medir qué tan rápido se mueve algo comparando la cantidad de diferencia entre frames.

- **Técnica:** Análisis cuantitativo de frame difference
- **Output:** Clasificación en "Inmóvil", "Lento", "Normal", "Rápido"
- **Aplicación visual:** Barra de velocidad, efectos que escalan con intensidad
- **Casos de uso:** Visualizaciones de energía, sistemas reactivos de color/forma

### **Opción 2: Trigger de Acción**

Sistema que responda solo cuando detecta movimiento ACTIVO (no presencia estática).

- **Técnica:** Background subtraction con detección de cambios significativos
- **Output:** Estado binario (movimiento/sin movimiento)
- **Aplicación visual:** Efectos que se activan/desactivan, cambios de estado visual
- **Casos de uso:** Instalaciones que "despiertan", sistemas de partículas reactivos

### **Opción 3: Contador de Gestos**

Detectar movimientos discretos como "waves" o gestos de mano.

- **Técnica:** Análisis de picos de actividad en frame difference
- **Output:** Conteo de gestos completados
- **Aplicación visual:** Acumulación de elementos, progresión visual
- **Casos de uso:** Juegos gestuales, narrativas interactivas acumulativas

### **Opción 4: Detector de Dirección**

Determinar hacia dónde se está moviendo algo.

- **Técnica:** Comparación regional de diferencias entre frames
- **Output:** Dirección predominante (Izquierda, Derecha, Arriba, Abajo)
- **Aplicación visual:** Flechas direccionales, flujo de elementos gráficos
- **Casos de uso:** Control de navegación visual, composiciones direccionales

### **Opción 5: Control por Movimiento**

Usar frame difference para controlar un elemento en pantalla.

- **Técnica:** Mapeo de intensidad de movimiento a parámetros de control
- **Output:** Valores continuos para control de elementos
- **Aplicación visual:** Elementos que siguen el movimiento, juegos controlados por gestos
- **Casos de uso:** Interfaces gestuales, experiencias de control corporal

## Requisitos del Proyecto

### **Creativos:**

1. Desarrollar una propuesta estética coherente y original
2. Crear al menos tres variaciones visuales basadas en diferentes interacciones/tipos de movimiento
3. Integrar de manera fluida la interacción corporal con la experiencia visual
4. Demostrar comprensión de principios de diseño generativo

### **Técnicos:**

1. Implementar correctamente algún método de detección de movimiento en p5.js
2. Crear un sistema de respuesta visual que reaccione en tiempo real al movimiento detectado
3. Asegurar fluidez de al menos 20 FPS en la experiencia final
4. Implementar controles para calibración (sensibilidad, threshold, etc.)

### **Documentales:**

1. Mantener bitácora del proceso con documentación semanal
2. Incluir referencias visuales, bocetos y decisiones de diseño
3. Documentar desafíos técnicos y sus soluciones
4. Registrar pruebas de usuario y iteraciones

## Evaluación del Proyecto

### **Etapa de Diseño (40% del total):**

- **Investigación y referentes (10%):** Calidad y relevancia de la investigación
- **Diseño conceptual (15%):** Originalidad y coherencia de la propuesta
- **Planificación técnica (15%):** Viabilidad y detalle del plan de implementación

### **Etapa de Implementación (60% del total):**

- **Funcionamiento técnico (25%):** Correcta implementación de detección de movimiento
- **Calidad visual (20%):** Estética y fluidez de la experiencia generativa
- **Integración e interacción (15%):** Cohesión entre detección y respuesta visual
