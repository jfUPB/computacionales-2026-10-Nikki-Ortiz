# Unidad 4

## Bitácora de proceso de aprendizaje

### ACTIVIDAD 01 

<details>

<summary>OPENFRAMEWORKS</summary>

### NOTAS SOBRE FUNCIONAMIENTO DE OPENFRAMEWORKS EN VISUAL STUDIO

En C++ a diferencia de C# declaramos clases en un archivo y definimos en otro,luego de crear la ventana hay una clase que se llama por default "ofBaseApp", en el apartado de  "ofApp.h" se declaran las clases y en el ofApp.cpp se definen. Las funciones de UPDATE y DRAW son de cuidado porque en DRAW SOLAMENTE se dibuja mientras que en update se actualizan las variables que se van a utilizar en draw para dibujar.

<img width="2491" height="1295" alt="imagen" src="https://github.com/user-attachments/assets/ec52733e-ea0d-432c-9ee4-f611a76e78fe" />

</details>

Aqui se muestra como comentario dentro del código de visual studio, el funcionamiento del estado keyPressed
<img width="2096" height="403" alt="Funcionamiento del código .cpp" src="https://github.com/user-attachments/assets/c387e86d-8a43-4bc3-8655-704b80cc2a4f" />

---
Después de compilar y ejecutar este es uno de los resultados que se puede obtener.
<img width="2160" height="1094" alt="Resulados al Ejecutar al Código" src="https://github.com/user-attachments/assets/25759820-658a-44fb-b8c7-05b233131edf" />



## Bitácora de aplicación 
 ### ACTIVIDAD 03 

 FIFO significa First In, First Out (Primero en entrar, primero en salir). 
 
 Un ejemplo de esste concepto puede ser la fila de una heladería:
 - La primera persona que llega a la fila es la primera en ser atendida y salir.
 - Si llega alguien nuevo, se pone al final de la fila.
 - Nadie se puede "colar" en medio.

En programación, a esta estructura la llamamos Cola (Queue).

Para empezar con el ```ofApp.h``` tomamos el código muestra que nos entregaron y agregamos: Todas las definiciones de las definiciones ya declaradas, y la logica de los punnteros dentro de las mismas; control del tamaño (agregar que si se pasa, borra al mas viejo) al final del enqueue

<details>

<summary>OfApp.h</summary>

### CÓDIGO CON LOS FALTANTES AGREGADOS

```c++
#pragma once
#include "ofMain.h"


struct Node { // Nodo de la cola 
	float x, y;
	float radius;
	ofColor color;
	float opacity;
	Node * next; // Es un puntero. Es como si en una fila cada persona tiene un brazo extendido tocando el hombro de la persona que tiene detrás. Así saben quién sigue

	Node(float _x, float _y, float _radius, ofColor _color, float _opacity) // funciona como una caja que va a guardar la informacion del pincel 
		: x(_x)
		, y(_y)
		, radius(_radius)
		, color(_color)
		, opacity(_opacity)
		, next(nullptr) {
	}
};


class BrushQueue { // Implementación manual de un Queue con el concepto de FIFO
public:
	Node * front; // El inicio de la fila (donde sacamos elementos). puntero 
	Node * rear; // El final de la fila (donde agregamos elementos). puntero
	int size;
	int maxSize;

	BrushQueue(int _maxSize);
	~BrushQueue();

	void enqueue(float x, float y, float radius, ofColor color, float opacity); // Si la fila ya tiene elementos, (rear) señala al nuevo nodo y luego convierte al nuevo nodo en el nuevo "último"
	void dequeue();
	void clear();
	bool isEmpty();
};


BrushQueue::BrushQueue(int _maxSize) // Constructor
	: front(nullptr)
	, rear(nullptr) // Cuando se crea la cola, empieza vacia, por lo que front y rear apuntan a null
	, size(0)
	, maxSize(_maxSize) { }


BrushQueue::~BrushQueue() { // Destructor
	clear(); // limpia la cola
}


void BrushQueue::enqueue(float x, float y, float radius, ofColor color, float opacity) { // Agregar un nuevo trazo al final de la cola
	Node * newNode = new Node(x, y, radius, color, opacity);

	if (isEmpty()) {
		front = rear = newNode;
	} else {
		rear->next = newNode;
		rear = newNode; // si esta vacia tanto front como rear apuntan al nuevo nodo
	}

	size++;

	if (size > maxSize) { // control de trazo o tamaño, si se pasa, ejecutar dequeue
		dequeue();
	}
}


void BrushQueue::dequeue() { // Eliminar el trazo más antiguo
	if (isEmpty()) {

		return;
	}

	Node * temp = front; // punero temporal que apunta a front
	front = front->next; // mueve a front para el que sigue en la fila
	delete temp; // elimina
	size--;

	if (front == nullptr) {
		rear = nullptr;
	}
}

// Eliminar todos los trazos
void BrushQueue::clear() {
	while (!isEmpty()) {
		dequeue();
	}
}

// Verificar si la cola está vacía
bool BrushQueue::isEmpty() {
	return front == nullptr;
}

class ofApp : public ofBaseApp {
public:
	BrushQueue strokes; // Cola de trazos
	float backgroundHue = 0;

	ofApp()
		: strokes(50) { } // Tamaño máximo de la cola

	void setup();
	void update();
	void draw();
	void keyPressed(int key);
};
```
</details>

Después, seguimos con el ```ofApp.cpp``` tomamos el código muestra que nos entregaron y lo modificamos para agregar la logica de todo.

<details>

<summary>OfApp.cpp</summary>

### CÓDIGO CON LOS FALTANTES AGREGADOS

```c++
#include "ofApp.h"

//--------------------------------------------------------------
void ofApp::setup() {
	ofBackground(0);
	//ofSetCircleResolution(40);
}

//--------------------------------------------------------------
void ofApp::update() {
	backgroundHue += 0.2;
	if (backgroundHue > 255) backgroundHue = 0;

	// TODO: agregar un nuevo trazo si el mouse está presionado.
	// Usa strokes.enqueue(x, y, radius, color, opacity);

	static int lastX = -1; // se usan para saber si el mouse se movió. Si la posición es distinta a la anterior se crea un trazo
	static int lastY = -1;

	int x = ofGetMouseX();
	int y = ofGetMouseY();

	if (x != lastX || y != lastY) { // Se genera un radio aleatorio (ofRandom) y un color al azar usando el sistema HSB (Tono, Saturación, Brillo).
		float radius = ofRandom(8, 22); 

		ofColor color;
		color.setHsb(ofRandom(255), 200, 255);

		float opacity = 180;

		strokes.enqueue(x, y, radius, color, opacity); // Se llama para implementar el concepto FIFO, cada vez que se mueve el mouse, se añade un "nodo" al final de la fila.

		lastX = x;
		lastY = y;
	}
}

//--------------------------------------------------------------
void ofApp::draw() {
	// Fondo con gradiente dinámico
	ofColor color1, color2;
	color1.setHsb(backgroundHue, 150, 240);
	color2.setHsb(fmod(backgroundHue + 128, 255), 150, 240);
	ofBackgroundGradient(color1, color2, OF_GRADIENT_LINEAR);

	// TODO: dibujar los trazos almacenados en la cola.
	// Recorre los nodos desde strokes.front hasta nullptr y usa ofDrawCircle().

	Node * current = strokes.front; // Puntero, empieza en strokes.front (el más viejo) y se mueve de uno en uno usando current = current->next hasta llegar a nullptr (el final).
	int index = 0;

	while (current != nullptr) {
		float alpha;

		if (strokes.size <= 1) {
			alpha = 255;
		} else {
			alpha = ofMap(index, 0, strokes.size - 1, 40, 255, true); // Se usa para que los trazos más antiguos sean más transparentes y los más nuevos (cercanos al final) sean más sólidos.
		}

		ofColor drawColor = current->color;
		drawColor.a = alpha;

		ofSetColor(drawColor);
		ofDrawCircle(current->x, current->y, current->radius);

		current = current->next;
		index++;
	}

}

//--------------------------------------------------------------
void ofApp::keyPressed(int key) {
	if (key == 'c') {
		// TODO: limpiar la cola de trazos.

		strokes.clear();
	}

	else if (key == 'a') {
		// TODO: alternar entre 50 y 100 trazos.

		if (strokes.maxSize == 50) {
			strokes.maxSize = 100;
		} else {
			strokes.maxSize = 50;
		}
		while (strokes.size > strokes.maxSize) {
			strokes.dequeue();
		}

	} else if (key == 's') {
		// TODO: guardar el frame actual.

		ofSaveFrame();
	}
}
```
</details>

**EVIDENCIAS:**

Todas las evidencias deben tener: 

- Captura de pantalla del depurador (mostrando el código detenido en el breakpoint y las variables/memoria de interés).
- Explicación: ¿Qué variables y valores específicos se están observando en la imagen?
- Justificación: ¿Por qué esto demuestra que la estructura o la función cumple con lo solicitado en el enunciado?


<details>

<summary>EVIDENCIA 1</summary>

### Evidencia 1: inserción del primer nodo en una cola vacía (enqueue)

<img width="2481" height="1002" alt="imagen" src="https://github.com/user-attachments/assets/434f5fa7-1b42-4e1c-8b1e-77e9a3010343" />

Se observa que antes de la inserción front y rear son NULL (o 0x0). Después de ejecutar la línea front = rear = newNode;, lo que significa que ambos punteros tienen la misma dirección de memoria y estan apuntando a el nodo nuevo gracias a el "If Empty". Esto demuestra que cuando la cola nace, el primer elemento es, al mismo tiempo, el principio y el fin de la fila porque está vacia y dentro del código se especifica que deben hacer los punteros en ese caso.

</details>

<details>

<summary>EVIDENCIA 2</summary>

###  Evidencia 2: mantenimiento del orden FIFO al insertar más nodos (enqueue)

Size 2:
<img width="2518" height="910" alt="imagen" src="https://github.com/user-attachments/assets/c51c4c50-73a9-4e28-a0bc-80b4e4518f9d" />

Size 3:
<img width="2534" height="885" alt="imagen" src="https://github.com/user-attachments/assets/fde35188-c1da-4f80-b71e-43cd3552a365" />

Size 4:
<img width="2469" height="1211" alt="imagen" src="https://github.com/user-attachments/assets/7c4a03cc-e228-42cb-98d7-335f9a4956eb" />


Se observa que cada nuevo nodo se agrega al final de la estructura mediante el puntero rear, mientras que front sigue apuntando al primer nodo insertado. Además, se evidencia que cada nodo mantiene un enlace al siguiente mediante next. El hecho de que cada nodo guarde la dirección del "siguiente" en el orden en que fueron creados demuestra que la estructura lineal se mantiene correctamente y demuestra que la cola mantiene el orden FIFO, ya que los nodos más antiguos permanecen en el frente mientras los nuevos se agregan al final

</details>

<details>

<summary>EVIDENCIA 3</summary>

### Evidencia 3: comportamiento de eliminación y prevención de fugas (dequeue)

ANTES:
<img width="1952" height="774" alt="Captura de pantalla 2026-03-13 133749" src="https://github.com/user-attachments/assets/5eef36e0-9d1e-4ca0-9238-90009be920cb" />

DESPUÉS:
<img width="1959" height="908" alt="imagen" src="https://github.com/user-attachments/assets/dc38461c-b099-4d89-b96c-223a7c05e7c4" />


El puntero temp que apunta a front almacena el nodo más antiguo de la cola, cuando front pasa a ser el elemento que sigue en la fila y el nodo más antiguo es eliminado con el delete. Esto demuestra que la eliminación respeta el orden FIFO y que la memoria del nodo eliminado se libera correctamente, evitando fugas de memoria. se ve como pasa a 51 y luego después de pasar el breakpoint vuelve a 50.

</details>

<details>

<summary>EVIDENCIA 4</summary>

### Evidencia 4: control del tamaño máximo de la cola (maxSize)

ANTES:
<img width="1848" height="790" alt="Captura de pantalla 2026-03-13 135257" src="https://github.com/user-attachments/assets/824b203a-3b5a-457f-913e-2165750b10f7" />

DESPUÉS:
<img width="1617" height="757" alt="imagen" src="https://github.com/user-attachments/assets/27e64d02-2b7b-4555-be70-0134e871c104" />

En la imagen se puede ver como el tamaño supero el tamaño máximo que esta establecido: En el momento en el que se ejecuta el dequeue() se elimina el nodo más antiguo y esto evita que la cola crezca descotroladamente y se limite a mantenerse en el tamaño definido

</details>

<details>

<summary>EVIDENCIA 5</summary>

### Evidencia 5: recorrido de la cola sin destruirla (draw)

VUELTA 1: 
<img width="2094" height="665" alt="imagen" src="https://github.com/user-attachments/assets/da48840e-ad01-450f-8fa1-737365fa252b" />

VUELTA 2: 

<img width="1997" height="894" alt="imagen" src="https://github.com/user-attachments/assets/ebd7391b-5494-49ce-a628-c4f54a65fc4c" />

Se observa en las  imagenes que el puntero current recorre todos los nodos de la cola, sin embargo, los punteros front y rear permanecen sin cambios y el tamaño de la cola no se modifica, lo cual nos dice que la  función draw() solo recorre la estructura para dibujar los trazos, sin modificar ni destruir la cola incluso despues de 2 pasadas o más de ser necesarias.

</details>

<details>

<summary>EVIDENCIA 6</summary>

### Evidencia 6: limpieza total de la memoria (clear)

ANTES:

<img width="1670" height="773" alt="imagen" src="https://github.com/user-attachments/assets/cb9f58d1-4467-45f5-8d1f-e95db0ee75e2" />

DESPUÉS:

<img width="1545" height="610" alt="imagen" src="https://github.com/user-attachments/assets/a4a9cca6-5c6b-4f39-b3e7-fb077fde6a1e" />
<img width="1498" height="210" alt="imagen" src="https://github.com/user-attachments/assets/527ef439-83b5-4151-8138-934dc1eb46a7" />

la imagen muestra como al precionar c y ejecutar la función clear(), el programa utiliza un bucle while que invoca dequeue() de forma ducesiva hasta vaciar la estructura, lo que garantiza que no queden fugas de memoria (memory leaks). Esto valida que la implementación cumple con el enunciado al asegurar que el programa pueda liberar todos sus recursos dinámicos, dejando la estructura BrushQueue en un estado limpio, consistente y listo para ser destruido por el destructor (~BrushQueue).

</details>















## Bitácora de reflexión

