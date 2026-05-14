# Unidad 6

## Bitácora de proceso de aprendizaje

### ACTIVIDAD 01

¿Qué observas en la aplicación al presionar las teclas a, r, s, n?

la letra `a` atrae las particulas donde esta el cursor, `r` repele las particulas al rededor del cursor, `s` detiene todo moviento de particulas y `n` las devuelve a su estado normal 

¿Qué diferencias notas entre los tipos de partículas?

las rojas tienen una mayor cantidad que las otras y en general son mas pequeñas, las verdes son las mas rapidas y las azules son mas grandes que las otras y son mas lentas

Formula una hipótesis: ¿Cómo crees que el código organiza la comunicación entre las teclas, las partículas y el cambio de comportamiento?

### ACTIVIDAD 02

### ACTIVIDAD 03


## Bitácora de aplicación 

### Actividad 04

**FASE 1**

<details>
<summary><b>ofApp.h</b></summary>

``` cpp
#pragma once

#include "ofMain.h"
#include <string>
#include <vector>

class Observer {
public:
	virtual ~Observer() = default;
	virtual void onNotify(const std::string & event) = 0;
};

class Subject {
public:
	void addObserver(Observer * observer);
	void removeObserver(Observer * observer);

protected:
	void notify(const std::string & event);

private:
	std::vector<Observer *> observers;
};

class Particle;

class State {
public:
	virtual ~State() = default;
	virtual void update(Particle * particle) = 0;
	virtual void onEnter(Particle * particle) { }
	virtual void onExit(Particle * particle) { }
};

class Particle : public Observer {
public:
	Particle();
	~Particle() override;

	Particle(const Particle &) = delete;
	Particle & operator=(const Particle &) = delete;

	void update();
	void draw();
	void onNotify(const std::string & event) override;

	void setState(State * newState);

	ofVec2f position;
	ofVec2f velocity;
	float size;
	ofColor color;

private:
	void keepInsideWindow();
	State * state;
};

class NormalState : public State {
public:
	void update(Particle * particle) override;
	void onEnter(Particle * particle) override;
};

class AttractState : public State {
public:
	void update(Particle * particle) override;
};

class RepelState : public State {
public:
	void update(Particle * particle) override;
};

class StopState : public State {
public:
	void update(Particle * particle) override;
};

class OrbitState : public State {
public:
	void update(Particle * particle) override;
};

class ParticleFactory {
public:
	static Particle * createParticle(const std::string & type);
};

class ofApp : public ofBaseApp, public Subject {
public:
	~ofApp() override;
	void setup() override;
	void update() override;
	void draw() override;
	void keyPressed(int key) override;

private:
	std::vector<Particle *> particles;
};

```
</details>

---

<details>
<summary><b>ofApp.cpp</b></summary>

``` cpp
#include "ofApp.h"
#include <algorithm>

void Subject::addObserver(Observer * observer) {
	if (!observer) return;
	if (std::find(observers.begin(), observers.end(), observer) == observers.end()) {
		observers.push_back(observer);
	}
}

void Subject::removeObserver(Observer * observer) {
	if (!observer) return;
	observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
}

void Subject::notify(const std::string & event) {
	for (Observer * observer : observers) {
		observer->onNotify(event);
	}
}

Particle::Particle()
	: state(nullptr) {
	position = ofVec2f(ofRandomWidth(), ofRandomHeight());
	velocity = ofVec2f(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
	size = ofRandom(2.0f, 5.0f);
	color = ofColor(255);

	state = new NormalState();
	state->onEnter(this);
}

Particle::~Particle() {
	if (state) {
		state->onExit(this);
		delete state;
		state = nullptr;
	}
}

void Particle::setState(State * newState) {
	if (state) {
		state->onExit(this);
		delete state;
	}
	state = newState;
	if (state) {
		state->onEnter(this);
	}
}

void Particle::update() {
	if (state) {
		state->update(this);
	}
	keepInsideWindow();
}

void Particle::draw() {
	ofPushStyle();
	ofSetColor(color);
	ofDrawCircle(position, size);
	ofPopStyle();
}

void Particle::onNotify(const std::string & event) {
	if (event == "attract") {
		setState(new AttractState());
	} else if (event == "repel") {
		setState(new RepelState());
	} else if (event == "stop") {
		setState(new StopState());
	} else if (event == "normal") {
		setState(new NormalState());
	} else if (event == "orbit") {
		setState(new OrbitState());
	}
}

void Particle::keepInsideWindow() {
	const float W = static_cast<float>(ofGetWidth());
	const float H = static_cast<float>(ofGetHeight());

	if (position.x < 0.0f) {
		position.x = 0.0f;
		velocity.x *= -1.0f;
	} else if (position.x > W) {
		position.x = W;
		velocity.x *= -1.0f;
	}
	if (position.y < 0.0f) {
		position.y = 0.0f;
		velocity.y *= -1.0f;
	} else if (position.y > H) {
		position.y = H;
		velocity.y *= -1.0f;
	}
}

void NormalState::onEnter(Particle * particle) {
	particle->velocity.set(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
}

void NormalState::update(Particle * particle) {
	particle->position += particle->velocity;
}

static void steer(Particle * particle, const ofVec2f & toward, float accel, float vmax, float posScale) {
	ofVec2f dir = toward - particle->position;
	float len = dir.length();
	if (len > 1e-6f) {
		dir /= len;
		particle->velocity += dir * accel;
	}
	particle->velocity.limit(vmax);
	particle->position += particle->velocity * posScale;
}

void AttractState::update(Particle * particle) {
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	steer(particle, mouse, 0.05f, 3.0f, 0.2f);
}

void RepelState::update(Particle * particle) {
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	ofVec2f away = particle->position - mouse;
	float len = away.length();
	if (len > 1e-6f) {
		away /= len;
		particle->velocity += away * 0.05f;
	}
	particle->velocity.limit(3.0f);
	particle->position += particle->velocity * 0.2f;
}

void StopState::update(Particle * particle) {
	particle->velocity *= 0.80f;
	if (particle->velocity.lengthSquared() < 1e-4f) {
		particle->velocity.set(0.0f, 0.0f);
	}
	particle->position += particle->velocity;
}

void OrbitState::update(Particle * particle) {
	ofVec2f center(ofGetWidth() * 0.5f, ofGetHeight() * 0.5f);
	ofVec2f dir = particle->position - center;
	float len = dir.length();
	if (len < 1e-6f) {
		dir.set(1.0f, 0.0f);
		len = 1.0f;
	}
	dir /= len;
	ofVec2f tangent(-dir.y, dir.x);
	particle->velocity = tangent * 2.5f;
	particle->position += particle->velocity;
}

Particle * ParticleFactory::createParticle(const std::string & type) {
	Particle * particle = new Particle();
	if (type == "star") {
		particle->size = ofRandom(2.0f, 4.0f);
		particle->color = ofColor(255, 0, 0);
	} else if (type == "shooting_star") {
		particle->size = ofRandom(3.0f, 6.0f);
		particle->color = ofColor(0, 255, 0);
		particle->velocity *= 3.0f;
	} else if (type == "planet") {
		particle->size = ofRandom(5.0f, 8.0f);
		particle->color = ofColor(0, 0, 255);
	} else if (type == "comet") {
		particle->size = ofRandom(6.0f, 7.0f);
		particle->color = ofColor(255, 140, 0);
		particle->velocity *= 6.0f;
	}
	return particle;
}

ofApp::~ofApp() {
	for (Particle * p : particles) {
		removeObserver(p);
		delete p;
	}
	particles.clear();
}

void ofApp::setup() {
	ofBackground(0);
	particles.reserve(100 + 5 + 10 + 8);

	for (int i = 0; i < 100; ++i) {
		Particle * p = ParticleFactory::createParticle("star");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 5; ++i) {
		Particle * p = ParticleFactory::createParticle("shooting_star");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 10; ++i) {
		Particle * p = ParticleFactory::createParticle("planet");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 8; ++i) {
		Particle * p = ParticleFactory::createParticle("comet");
		particles.push_back(p);
		addObserver(p);
	}
}

void ofApp::update() {
	for (Particle * p : particles) {
		p->update();
	}
}

void ofApp::draw() {
	for (Particle * p : particles) {
		p->draw();
	}
}

void ofApp::keyPressed(int key) {
	switch (key) {
	case 's':
		notify("stop");
		break;
	case 'a':
		notify("attract");
		break;
	case 'r':
		notify("repel");
		break;
	case 'n':
		notify("normal");
		break;
	case 'o':
		notify("orbit");
		break;
	default:
		break;
	}
}

```
</details>

---

**FASE 2**

<details>
<summary><b>Evidencia 1 — Tu nueva partícula en la Factory</b></summary>

MEMORIA LOCAL:
<img width="1915" height="1051" alt="imagen" src="https://github.com/user-attachments/assets/3faf82ba-f375-4d1d-a5a8-705ec9b53603" />

---

MEMORIA AUTOS:

<img width="1927" height="598" alt="imagen" src="https://github.com/user-attachments/assets/67447e7b-eb23-4502-bd83-a7554918afbb" />

Ese punto resulta importante porque permite comprobar si la factory identificó correctamente el nuevo tipo y si el objeto creado fue inicializado de manera adecuada.

En el depurador puede verse que el parámetro `type` tiene el valor `"comet"`, lo que provoca la ejecución de la nueva condición dentro de la factory. El puntero `particle` referencia un objeto recién reservado en memoria, cuyos atributos reflejan la configuración particular de este nuevo tipo: un tamaño más grande, color naranja y una velocidad superior a la de una partícula convencional.

La evidencia muestra que la creación del objeto no está distribuida en distintas partes del programa, sino concentrada en `ParticleFactory::createParticle`. También demuestra que la nueva partícula no existe solo como un nombre agregado al sistema, sino que sale de la factory con características y comportamientos propios.

</details>  

---

<details>
<summary><b>Evidencia 2 — Tu nuevo estado en la _vtable</b></summary><br>

IMPORTANTE:

<img width="676" height="262" alt="imagen" src="https://github.com/user-attachments/assets/7bcb1e07-82ed-4a53-98fb-95556848cc02" />

---

NormalState
<img width="1929" height="868" alt="imagen" src="https://github.com/user-attachments/assets/d6da9c72-cd2a-47d5-a70a-ee801a73237a" />

---

OrbitState
<img width="1932" height="919" alt="imagen" src="https://github.com/user-attachments/assets/ae3aeaa4-90d5-4510-9f78-16306990eaba" />

La comparación evidencia que ambos objetos utilizan la misma interfaz base `State`, aunque cada uno posee una tabla virtual diferente. En `NormalState`, los métodos redefinidos son el destructor, `NormalState::update` y `NormalState::onEnter`. En cambio, en `OrbitState`, la `vtable` queda conformada por el destructor, `OrbitState::update`, además de `State::onEnter` y `State::onExit`, heredados de la clase base.

Esto confirma que el patrón State funciona mediante polimorfismo, ya que el puntero `state` continúa siendo de tipo `State*`, pero al cambiar el objeto al que referencia también cambia la `vtable` asociada. Gracias a esto, una misma instrucción:

```cpp
state->update(this);
```

puede ejecutar comportamientos distintos dependiendo del estado que se encuentre activo en ese momento.

</details>

---

<details>
<summary><b>Evidencia 3 — La cadena Observer → State completa</b></summary>

Esos puntos resultan importantes porque permiten observar el flujo completo del evento, desde la acción realizada por el usuario hasta la modificación del estado interno del sistema.

en `keyPressed`: `key = 'o'`
<img width="1924" height="645" alt="imagen" src="https://github.com/user-attachments/assets/fe7f3985-1413-46a5-beb9-1f86793d1f07" />

---

en `notify`: `event = "orbit"`
<img width="1906" height="807" alt="imagen" src="https://github.com/user-attachments/assets/20303018-74fb-41fe-aaf1-88b2a588d8f7" />

---

en `onNotify`: `event = "orbit"`
<img width="1911" height="1057" alt="imagen" src="https://github.com/user-attachments/assets/e6832905-95b3-4fd2-9302-aa39a5944453" />
<img width="1924" height="1084" alt="imagen" src="https://github.com/user-attachments/assets/8cbbf257-9e9d-4a53-a6b3-598dfacba3b2" />

---


en `setState`: `newState = State* {OrbitState}`

<img width="1906" height="1192" alt="imagen" src="https://github.com/user-attachments/assets/0756a7f3-c859-430d-a445-8cf146277448" />
<img width="1878" height="1164" alt="imagen" src="https://github.com/user-attachments/assets/eafab887-e76d-46ee-9523-353a4b4d4dd2" />
<img width="1908" height="1195" alt="imagen" src="https://github.com/user-attachments/assets/83e7e8b0-ffc4-4fc5-9a88-059626390438" />
<img width="1888" height="1036" alt="imagen" src="https://github.com/user-attachments/assets/ad480255-7ce9-499a-b271-5863cafb2d3f" />


Cuando se presiona la tecla `o`, el método `keyPressed` envía el evento `notify("orbit")`. Este evento es recibido por el `Subject`, que recorre la lista de observadores y ejecuta `onNotify(event)` en cada partícula. Luego, dentro de `Particle::onNotify`, el evento `"orbit"` selecciona la condición correspondiente y llama a `setState(new OrbitState())`. Finalmente, en `setState`, el puntero `state` deja de referenciar el estado anterior y comienza a apuntar al nuevo objeto `OrbitState`.

La evidencia permite ver que no se trata de una acción independiente, sino de una interacción coordinada entre varios patrones de diseño. El patrón `Observer` se encarga de distribuir el evento desde la entrada del usuario hacia todas las partículas, mientras que el patrón `State` utiliza ese evento para sustituir el estado activo y modificar el comportamiento de las partículas en tiempo de ejecución.

</details>

---

<details>
<summary><b>Evidencia 4 — Decisión de diseño justificada</b></summary>

Se decidió que `OrbitState` heredara directamente de `State` y no de `AttractState` o `NormalState`, ya que representa un comportamiento independiente y completo, en lugar de una simple modificación o extensión de otro estado existente.

<img width="939" height="582" alt="Captura de pantalla 2026-05-14 080235" src="https://github.com/user-attachments/assets/e8ca4727-1491-41a1-b27a-1c18057a4f2c" />
<img width="954" height="598" alt="Captura de pantalla 2026-05-14 080314" src="https://github.com/user-attachments/assets/88f0aba7-ca93-4070-ae7b-dcb0b2a8fef7" />

Antes de realizar la asignación, la partícula ya posee un estado activo (`NormalState`) y, al mismo tiempo, existe en memoria un nuevo objeto (`OrbitState`) preparado para convertirse en el siguiente estado. Ambos objetos son independientes y cuentan con su propia `vtable`, lo que significa que cada uno define comportamientos diferentes.

Durante la ejecución de `setState`, primero se ejecuta `onExit` sobre el estado actual y luego este se elimina mediante `delete`. Después de eso, el puntero `state` se actualiza para referenciar el nuevo objeto `OrbitState`.

Una vez completada la asignación, `state` deja de apuntar al estado anterior y pasa a referenciar únicamente al nuevo estado, que será el responsable del comportamiento de la partícula en las próximas actualizaciones.

La decisión de diseño consistió en implementar los cambios de comportamiento creando un nuevo objeto de estado y reemplazando el puntero `state`, en vez de alterar variables internas de la partícula o depender de múltiples condicionales.

Esta solución resulta adecuada porque:

* mantiene los comportamientos de cada estado aislados y organizados
* evita el uso de condicionales extensos dentro de `Particle`
* hace más sencillo ampliar el sistema agregando nuevos estados sin modificar la lógica existente
* utiliza polimorfismo y despacho dinámico para cambiar comportamientos en tiempo de ejecución


</details>

## Bitácora de reflexión
