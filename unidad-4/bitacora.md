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

Después, seguimos con el ```ofApp.cpp``` tomamos el código muestra que nos entregaron y lo modificamos 

## Bitácora de reflexión

