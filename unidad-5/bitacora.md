# Unidad 5
## Bitácora de proceso de aprendizaje
### ACTIVIDAD 01
### ACTIVIDAD 02
### ACTIVIDAD 03

<img width="2704" height="1840" alt="image" src="https://github.com/user-attachments/assets/a237ec38-cf98-4af4-ae96-3d296b6bf577" />

---

### ACTIVIDAD 04
### ACTIVIDAD 05
## Bitácora de aplicación 

### ACTIVIDAD 06

**FASE 01**

<details>

<summary>ofApp.h</summary>

### ofApp.h SIN comentarios

```C++
  #pragma once
#include "ofMain.h"
#include <vector>

class Particle {
public:
	virtual ~Particle() { }
	virtual void update(float dt) = 0;
	virtual void draw() = 0;
	virtual bool isDead() const = 0;
	virtual bool shouldExplode() const { return false; }
	virtual glm::vec2 getPosition() const { return glm::vec2(0, 0); }
	virtual ofColor getColor() const { return ofColor(255); }
};

class RisingParticle : public Particle {
protected:
	glm::vec2 position;
	glm::vec2 velocity;
	ofColor color;
	float lifetime;
	float age;
	bool exploded;

public:
	RisingParticle(const glm::vec2 & pos, const glm::vec2 & vel,
		const ofColor & col, float life)
		: position(pos)
		, velocity(vel)
		, color(col)
		, lifetime(life)
		, age(0)
		, exploded(false) { }

	void update(float dt) override {
		position += velocity * dt;
		age += dt;
		velocity.y += 9.8f * dt * 8;
		float explosionThreshold = ofGetHeight() * 0.15f + ofRandom(-30, 30);
		if (position.y <= explosionThreshold || age >= lifetime) {
			exploded = true;
		}
	}

	void draw() override {
		ofSetColor(color);
		ofDrawCircle(position, 10);
	}

	bool isDead() const override { return exploded; }
	bool shouldExplode() const override { return exploded; }
	glm::vec2 getPosition() const override { return position; }
	ofColor getColor() const override { return color; }
};

class OscillatingParticle : public Particle {
private:
	glm::vec2 position;
	glm::vec2 velocity;
	ofColor color;
	float lifetime;
	float age;
	bool exploded;
	float frequency;
	float amplitude;
	float baseX;

public:
	OscillatingParticle(const glm::vec2 & pos, const glm::vec2 & vel,
		const ofColor & col, float life)
		: position(pos)
		, velocity(vel)
		, color(col)
		, lifetime(life)
		, age(0)
		, exploded(false)
		, frequency(ofRandom(6.0f, 10.0f))
		, amplitude(ofRandom(40.0f, 90.0f))
		, baseX(pos.x) { }

	void update(float dt) override {
		age += dt;
		position.y += velocity.y * dt;
		position.x = baseX + sin(age * frequency) * amplitude;
		velocity.y += 9.8f * dt * 7;
		float explosionThreshold = ofGetHeight() * 0.18f + ofRandom(-20, 20);
		if (position.y <= explosionThreshold || age >= lifetime) {
			exploded = true;
		}
	}

	void draw() override {
		ofSetColor(color);
		ofDrawTriangle(position.x, position.y - 12,
			position.x - 8, position.y + 8,
			position.x + 8, position.y + 8);
	}

	bool isDead() const override { return exploded; }
	bool shouldExplode() const override { return exploded; }
	glm::vec2 getPosition() const override { return position; }
	ofColor getColor() const override { return color; }
};

class SwirlParticle : public Particle {
private:
	glm::vec2 center;
	glm::vec2 velocity;
	ofColor color;
	float lifetime;
	float age;
	bool exploded;
	float angle;
	float radius;
	float angularSpeed;

public:
	SwirlParticle(const glm::vec2 & pos, const glm::vec2 & vel,
		const ofColor & col, float life)
		: center(pos)
		, velocity(vel)
		, color(col)
		, lifetime(life)
		, age(0)
		, exploded(false)
		, angle(0)
		, radius(0)
		, angularSpeed(ofRandom(4.0f, 8.0f)) { }

	void update(float dt) override {
		age += dt;
		center += velocity * dt;
		velocity.y += 9.8f * dt * 6;
		angle += angularSpeed * dt;
		radius += 40.0f * dt;
		float explosionThreshold = ofGetHeight() * 0.2f + ofRandom(-25, 25);
		if (center.y <= explosionThreshold || age >= lifetime) {
			exploded = true;
		}
	}

	void draw() override {
		glm::vec2 drawPos = center + glm::vec2(cos(angle), sin(angle)) * radius;
		ofSetColor(color);
		ofDrawCircle(drawPos, 7);
		ofDrawCircle(center, 3);
	}

	bool isDead() const override { return exploded; }
	bool shouldExplode() const override { return exploded; }
	glm::vec2 getPosition() const override { return center; }
	ofColor getColor() const override { return color; }
};

class ExplosionParticle : public Particle {
protected:
	glm::vec2 position;
	glm::vec2 velocity;
	ofColor color;
	float age;
	float lifetime;
	float size;

public:
	ExplosionParticle(const glm::vec2 & pos, const glm::vec2 & vel,
		const ofColor & col, float life, float sz)
		: position(pos)
		, velocity(vel)
		, color(col)
		, age(0)
		, lifetime(life)
		, size(sz) { }

	void update(float dt) override {
		position += velocity * dt;
		age += dt;
		float alpha = ofMap(age, 0, lifetime, 255, 0, true);
		color.a = alpha;
	}

	bool isDead() const override { return age >= lifetime; }
};

class CircularExplosion : public ExplosionParticle {
public:
	CircularExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.2f, ofRandom(16, 32)) {
		float angle = ofRandom(0, TWO_PI);
		float speed = ofRandom(80, 200);
		velocity = glm::vec2(cos(angle), sin(angle)) * speed;
	}

	void draw() override {
		ofSetColor(color);
		ofDrawCircle(position, size);
	}
};

class RandomExplosion : public ExplosionParticle {
public:
	RandomExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.5f, ofRandom(16, 32)) {
		velocity = glm::vec2(ofRandom(-200, 200), ofRandom(-200, 200));
	}

	void draw() override {
		ofSetColor(color);
		ofDrawRectangle(position.x, position.y, size, size);
	}
};

class StarExplosion : public ExplosionParticle {
public:
	StarExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.3f, ofRandom(20, 40)) {
		float angle = ofRandom(0, TWO_PI);
		float speed = ofRandom(90, 180);
		velocity = glm::vec2(cos(angle), sin(angle)) * speed;
	}

	void draw() override {
		ofSetColor(color);
		int rays = 5;
		float outerRadius = size;
		float innerRadius = size * 0.5f;
		ofPushMatrix();
		ofTranslate(position);
		for (int i = 0; i < rays; i++) {
			float theta = ofMap(i, 0, rays, 0, TWO_PI);
			float xOuter = cos(theta) * outerRadius;
			float yOuter = sin(theta) * outerRadius;
			float xInner = cos(theta + PI / rays) * innerRadius;
			float yInner = sin(theta + PI / rays) * innerRadius;
			ofDrawLine(0, 0, xOuter, yOuter);
			ofDrawLine(xOuter, yOuter, xInner, yInner);
		}
		ofPopMatrix();
	}
};

class WaveExplosion : public ExplosionParticle {
public:
	WaveExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.4f, ofRandom(10, 18)) {
		float angle = ofRandom(0, TWO_PI);
		float speed = ofRandom(140, 220);
		velocity = glm::vec2(cos(angle), sin(angle)) * speed;
	}

	void draw() override {
		ofNoFill();
		ofSetColor(color);
		ofSetLineWidth(2);
		ofDrawCircle(position, size);
		ofFill();
	}
};

class ofApp : public ofBaseApp {
public:
	void setup();
	void update();
	void draw();
	void mousePressed(int x, int y, int button);
	void keyPressed(int key);

	std::vector<Particle *> particles;
	~ofApp();

private:
	void createRisingParticle();
	void createOscillatingParticle();
	void createSwirlParticle();
};
```

</details>

**FASE 01**

<details>

<summary>ofApp.cpp</summary>

### ofApp.cpp SIN comentarios

```C++
  #include "ofApp.h"

void ofApp::setup() {
	ofSetFrameRate(60);
	ofBackground(0);
}

void ofApp::update() {
	float dt = ofGetLastFrameTime();

	for (int i = 0; i < particles.size(); i++) {
		particles[i]->update(dt);
	}

	for (int i = particles.size() - 1; i >= 0; i--) {
		if (particles[i]->shouldExplode()) {
			int explosionType = (int)ofRandom(4);
			int numParticles = (int)ofRandom(20, 30);

			for (int j = 0; j < numParticles; j++) {
				if (explosionType == 0) {
					particles.push_back(new CircularExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				} else if (explosionType == 1) {
					particles.push_back(new RandomExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				} else if (explosionType == 2) {
					particles.push_back(new StarExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				} else {
					particles.push_back(new WaveExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				}
			}

			delete particles[i];
			particles.erase(particles.begin() + i);
		} else if (particles[i]->isDead()) {
			delete particles[i];
			particles.erase(particles.begin() + i);
		}
	}
}

void ofApp::draw() {
	for (int i = 0; i < particles.size(); i++) {
		particles[i]->draw();
	}
}

void ofApp::createRisingParticle() {
	float minX = ofGetWidth() * 0.35f;
	float maxX = ofGetWidth() * 0.65f;
	float spawnX = ofRandom(minX, maxX);
	glm::vec2 pos(spawnX, ofGetHeight());
	glm::vec2 target(ofGetWidth() / 2.0f + ofRandom(-300, 300),
		ofGetHeight() * 0.10f + ofRandom(-30, 30));
	glm::vec2 direction = glm::normalize(target - pos);
	glm::vec2 vel = direction * ofRandom(250, 350);
	ofColor col;
	col.setHsb(ofRandom(255), 220, 255);
	float lifetime = ofRandom(1.5f, 3.5f);
	particles.push_back(new RisingParticle(pos, vel, col, lifetime));
}

void ofApp::createOscillatingParticle() {
	float spawnX = ofRandom(ofGetWidth() * 0.25f, ofGetWidth() * 0.75f);
	glm::vec2 pos(spawnX, ofGetHeight());
	glm::vec2 vel(0, ofRandom(-320, -260));
	ofColor col;
	col.setHsb(ofRandom(255), 200, 255);
	float lifetime = ofRandom(1.6f, 3.0f);
	particles.push_back(new OscillatingParticle(pos, vel, col, lifetime));
}

void ofApp::createSwirlParticle() {
	float spawnX = ofRandom(ofGetWidth() * 0.3f, ofGetWidth() * 0.7f);
	glm::vec2 pos(spawnX, ofGetHeight());
	glm::vec2 target(ofGetWidth() / 2.0f + ofRandom(-200, 200),
		ofGetHeight() * 0.15f + ofRandom(-20, 20));
	glm::vec2 direction = glm::normalize(target - pos);
	glm::vec2 vel = direction * ofRandom(220, 300);
	ofColor col;
	col.setHsb(ofRandom(255), 180, 255);
	float lifetime = ofRandom(1.8f, 3.2f);
	particles.push_back(new SwirlParticle(pos, vel, col, lifetime));
}

void ofApp::mousePressed(int x, int y, int button) {
	int type = (int)ofRandom(3);

	if (type == 0) {
		createRisingParticle();
	} else if (type == 1) {
		createOscillatingParticle();
	} else {
		createSwirlParticle();
	}
}

void ofApp::keyPressed(int key) {
	if (key == ' ') {
		for (int i = 0; i < 1000; i++) {
			int type = (int)ofRandom(3);
			if (type == 0) {
				createRisingParticle();
			} else if (type == 1) {
				createOscillatingParticle();
			} else {
				createSwirlParticle();
			}
		}
	}

	if (key == '1') {
		createRisingParticle();
	}

	if (key == '2') {
		createOscillatingParticle();
	}

	if (key == '3') {
		createSwirlParticle();
	}

	if (key == 's') {
		ofSaveScreen("screenshot_" + ofToString(ofGetFrameNum()) + ".png");
	}
}

ofApp::~ofApp() {
	for (int i = 0; i < particles.size(); i++) {
		delete particles[i];
	}
	particles.clear();
}
```

</details>

**PARTICULAS NUEVAS CREADAS:**
- OscillatingParticle
- SwirlParticle

**EXPLOSIÓN NUEVA:**
- WaveExplosion

---

**FASE 2:**

**Evidencias para RAE 1 — Patrones y estrategias OOP**

<details>

<summary> Evidencia 1 </summary>

### Herencia en memoria

<img width="1644" height="1215" alt="imagen" src="https://github.com/user-attachments/assets/5b32ea47-3c42-4de7-a373-1924e69f8f34" />

**Elección del punto de inspección**

Se colocó un breakpoint en el archivo `ofApp.cpp`, dentro de la función `createOscillatingParticle()`, que es la primera partícula nueva, específicamente en la línea después de `particles.push_back(new OscillatingParticle(pos, vel, col, lifetime));`. Este punto se eligió porque es el momento exacto en el que se crea el objeto en memoria y se inserta en el vector, lo que permite observar su estructura interna completa desde el inicio.

**Explicación**

En la imagen del depurador se puede observar que el vector `particles` tiene un tamaño de 1 y contiene un elemento de tipo `Particle*`, pero al expandir ese elemento se identifica claramente que el objeto real es de tipo `OscillatingParticle`. Dentro de este objeto se muestran todos sus atributos, como `position`, `velocity`, `color`, `lifetime`, `age` y `exploded`. También se observa una sección etiquetada como `Particle`, lo que indica la presencia de la clase base dentro de la estructura del objeto. 

**Justificación**

Esta demostración permite observar que aunque el vector almacena punteros de tipo `Particle*`, el objeto real conserva toda la información de la clase derivada `OscillatingParticle`. Se evidencia que la herencia no crea estructuras separadas, sino que organiza los datos en un único objeto que incluye tanto la base como la extensión. Esto confirma el funcionamiento del modelo de herencia en C++, donde un objeto derivado contiene internamente la parte base y sus propios atributos adicionales.

</details>
	
---

<details>
	
<summary> Evidencia 2 </summary>

### La _vtable de tu nuevo tipo

<img width="2132" height="741" alt="imagen" src="https://github.com/user-attachments/assets/d89eb622-950f-4f73-b51b-72a1ef33e75c" />
<img width="1905" height="975" alt="imagen" src="https://github.com/user-attachments/assets/3e5bc303-7d9e-473d-b616-d947f25126d7" />


**Elección del punto de inspección**
Se colocó un breakpoint en el archivo `ofApp.cpp`, dentro de la función `update()`, específicamente en la línea `particles.push_back(new CircularExplosion(...));`. Elegí este punto porque es el momento exacto en el que se crea una partícula de explosión y se agrega al vector `particles`. Esto permite inspeccionar el objeto justo cuando está siendo construido y verificar qué tabla virtual se le asigna, antes de que empiece a ejecutarse dentro del programa.

**Explicación**
Cuando la ejecución se detiene en esta línea, en el depurador se puede inspeccionar el contenido del vector `particles` y expandir el objeto recién creado. Allí se observa que, aunque el vector almacena punteros de tipo `Particle*`, el objeto real corresponde a `CircularExplosion`. Dentro de su estructura aparece el campo `_vfptr`, que apunta a la tabla virtual específica de ese tipo. Al expandirla, se muestran varias entradas que corresponden a funciones como el destructor, `update()`, `draw()` e `isDead()`. Esto demuestra que cada objeto guarda internamente una referencia a su propia tabla de funciones virtuales, la cual será usada para decidir qué método ejecutar.

**Justificación**
Esta evidencia demuestra la comprensión de cómo funciona la _vtable en C++, ya que permite observar directamente que el objeto creado no utiliza una tabla genérica de `Particle`, sino una tabla específica de `CircularExplosion`. Esto confirma que el polimorfismo se implementa mediante tablas virtuales diferentes para cada clase derivada. Además, evidencia que aunque el programa trabaje con punteros de tipo base, cada objeto conserva la información necesaria para ejecutar sus propios métodos correctamente en tiempo de ejecución.

Diferencias e igualddes en las entradas:

Para CircularExplosion:

- destructor → CircularExplosion
- update → ExplosionParticle::update
- draw → CircularExplosion::draw
- isDead → ExplosionParticle::isDead
- shouldExplode → Particle::shouldExplode
- getPosition → Particle::getPosition
- getColor → Particle::getColor

Para WaveExplosion:

- destructor → WaveExplosion
- update → ExplosionParticle::update
- draw → WaveExplosion::draw
- isDead → ExplosionParticle::isDead
- shouldExplode → Particle::shouldExplode
- getPosition → Particle::getPosition
- getColor → Particle::getColor

Se observa que ambas clases comparten varias funciones heredadas, pero la función draw() apunta a implementaciones distintas dependiendo del tipo real del objeto

</details>

--- 

<details>

<summary> Evidencia 3 </summary>

### Polimorfismo en tiempo de ejecución

<img width="1862" height="665" alt="Captura de pantalla 2026-05-06 095812" src="https://github.com/user-attachments/assets/037e2d68-8113-41d2-af32-6158d23724f1" />

<img width="1902" height="864" alt="imagen" src="https://github.com/user-attachments/assets/318ea95d-da75-409f-84b3-4d7f20b1a62d" />


### **Elección del punto de inspección**

Para esta evidencia coloqué el breakpoint en `ofApp.cpp`, específicamente en la línea `particles[i]->draw();`. Elegí este punto porque es donde el programa dibuja cada partícula usando un puntero de tipo base (`Particle*`), sin saber explícitamente cuál es su tipo real. Este momento es clave para comprobar si el polimorfismo en tiempo de ejecución está funcionando, ya que el método `draw` debe ejecutarse según la clase derivada correspondiente.

### **Explicación**

En el depurador se observa el vector `particles`, donde cada elemento está almacenado como `Particle*`, pero al expandirlos se puede ver su tipo real, como `CircularExplosion`, `RisingParticle` u `OscillatingParticle`.

Cuando el programa se detiene en la línea `particles[i]->draw();`, dependiendo del índice `i`, se está trabajando con un tipo distinto de partícula. Si se usa “Step Into” (F11), el flujo del programa entra directamente al método `draw` de la clase específica del objeto. 

### **Justificación**

Esta evidencia demuestra el polimorfismo en tiempo de ejecución porque un mismo llamado (`particles[i]->draw();`) produce resultados distintos dependiendo del tipo real del objeto. Aunque el vector almacena punteros de tipo `Particle*`, el programa decide dinámicamente qué versión del método `draw` ejecutar.

El uso del breakpoint en esta línea permite comprobar que el despacho dinámico funciona correctamente, ya que el flujo entra a diferentes implementaciones del método según la clase derivada. Esto confirma que el diseño orientado a objetos está bien aplicado y que cada tipo de partícula mantiene su comportamiento específico incluso cuando se maneja a través de la clase base.

</details> 

---  

<details>
<summary> Evidencia 4 </summary>

### — Encapsulamiento en el contexto de herencia

<img width="1914" height="834" alt="Captura de pantalla 2026-05-06 105427" src="https://github.com/user-attachments/assets/c5057fe1-b7f3-4e7e-9671-ba40bcbc7c4a" />

### **Elección del punto de inspección**

Coloqué el breakpoint en `ofApp.h`, dentro de `void draw() override` de `WaveExplosion`, específicamente en la línea `ofDrawCircle(position, size);`. Elegí este punto porque es una instrucción ejecutable donde se usan atributos heredados, lo que permite que el depurador muestre correctamente sus valores. En la declaración del método no funcionaba porque no es una línea ejecutable.

### **Explicación**

Al ejecutar el programa en debug y generar partículas, el breakpoint se activó dentro de `WaveExplosion::draw()`. En la ventana de Locals, al expandir `this`, se observaron los atributos del objeto, los cuales provienen de la clase base `ExplosionParticle`.

* **Protegidos / públicos (visibles en la subclase):**

  * `position`
  * `velocity`
  * `color`
  * `age`
  * `lifetime`
  * `size`

* **Privados (no visibles):**

  * Atributos definidos como `private` en otras clases o fuera de la jerarquía

### **Justificación**

Esto demuestra el encapsulamiento porque la subclase solo puede acceder a los atributos permitidos (`protected`), mientras que los privados no son visibles. Además, el depurador refleja esto mostrando únicamente los datos accesibles dentro de `this`, confirmando cómo se controla el acceso a la información en la jerarquía de herencia.

</details>

---

**Evidencias para RAE 2 — Pruebas y depuración**

<details>
<summary> Evidencia 5 </summary>

### —  Ciclo de vida completo de tu partícula

FASE 1 — CREACIÓN DE LA PARTÍCULA
<img width="2278" height="964" alt="imagen" src="https://github.com/user-attachments/assets/3bd40c67-f36f-42b6-af94-84b645f5faf7" />

--- 

FASE 2 — UPDATE / VIDA ACTIVA

<img width="1927" height="919" alt="imagen" src="https://github.com/user-attachments/assets/9f30b6f4-bda6-440e-bd57-3376521a8059" />

---

FASE 3 — ELIMINACIÓN / MUERTE

<img width="957" height="417" alt="Captura de pantalla 2026-05-06 224434" src="https://github.com/user-attachments/assets/70c9f138-036a-4fa8-8937-7632bbeaff51" />

<img width="1918" height="798" alt="imagen" src="https://github.com/user-attachments/assets/96069722-e62f-4b25-b257-3a29aa8e736d" />

--- 

**Se colocaron breakpoints en tres momentos del ciclo de vida de una partícula:**

- durante su creación, cuando se agrega al vector `particles`
- durante su actualización, en el método `update()`
- durante su eliminación, cuando se detecta que ha muerto y se libera su memoria

**En la primera captura** se observa la creación de la partícula mediante `new` y su inserción en el vector `particles`. El depurador muestra el tamaño del vector y el tipo dinámico del objeto creado.

**En la segunda captura** se observa el objeto durante su ejecución en el método `update()`. Se puede ver cómo cambian variables como la posición, la velocidad y la edad (`age`), lo que demuestra que el objeto mantiene estado y evoluciona en el tiempo.

**En las últimas capturas** se observa cómo el objeto se elimina cuando cumple su condición de muerte (`isDead()`): se ejecuta `delete` y, posteriormente, se elimina del vector. El tamaño del vector disminuye y el puntero deja de existir.

**Esta evidencia demuestra comprensión del ciclo de vida de los objetos dinámicos** porque muestra las tres etapas fundamentales: creación en memoria dinámica, modificación del estado durante la ejecución y liberación correcta de memoria al eliminar el objeto. Esto confirma que la implementación gestiona correctamente la memoria y evita objetos persistentes innecesarios

</details> 

--- 

<details>

<summary> Evidencia  6 </summary>

### Sin fugas de memoria

PRUEBA 1:
img width="2118" height="1239" alt="imagen" src="https://github.com/user-attachments/assets/362a0edd-c9c5-4e12-a462-24921b7b3fa5" />

---

PRUEBA 2:
<img width="1993" height="1240" alt="imagen" src="https://github.com/user-attachments/assets/f4c6d421-66b0-4edd-a91a-06afda103aa6" />

---

PRUEBA 3:
<img width="1944" height="1207" alt="imagen" src="https://github.com/user-attachments/assets/3756222c-3619-415e-a35f-aeee49eb157e" />

---

Se colocaron breakpoints en las instrucciones `delete particles[i];` y `particles.erase(particles.begin() + i);` porque estas dos líneas se encargan de liberar la memoria dinámica y retirar el puntero del contenedor.

En la primera captura se observa que el vector particles todavía contiene un puntero válido a la partícula que se va a eliminar. En ese momento, el tamaño del vector aún incluye ese elemento. Luego, al ejecutar `delete particles[i];`, se libera la memoria dinámica ocupada por el objeto. Después, en la instrucción `particles.erase(particles.begin() + i);`, el puntero correspondiente se elimina del vector. En la última captura se observa que el tamaño del vector disminuye y que el elemento ya no se encuentra almacenado en la posición anterior.

Esta evidencia demuestra que no hay fuga de memoria porque la implementación realiza correctamente las dos acciones necesarias al destruir una partícula: primero libera la memoria reservada dinámicamente con `delete` y luego elimina el puntero del vector con `erase`. Si faltara cualquiera de estas dos operaciones, habría un problema: una fuga de memoria o un puntero colgante dentro del contenedor

</details>

---

<details>

<summary> Evidencia  7 </summary>

### Prueba de condición límite

MODIFICACIÓN EN EL CÓDIGO:

<img width="1200" height="508" alt="imagen" src="https://github.com/user-attachments/assets/7758c9ab-97f1-4418-bc21-78d19cb74eee" />

---

TAMAÑO NUEVO DESPUES DE PRECIONAR ESPACIO:
<img width="1923" height="898" alt="imagen" src="https://github.com/user-attachments/assets/120adf29-334a-4025-9cc2-3550d2315c83" />

---

MAS DE 2000

<img width="1348" height="712" alt="imagen" src="https://github.com/user-attachments/assets/357f9534-32f2-4b4b-95c3-a61c3004c1e5" />

--- 
CAMBIO AL ELIMINAR PARTICULAS:

<img width="1801" height="702" alt="imagen" src="https://github.com/user-attachments/assets/a98928b0-f264-450a-bbf8-a18087f4b40e" />

---

<img width="1912" height="703" alt="imagen" src="https://github.com/user-attachments/assets/6a0644f3-04d3-443f-b367-11bc6e8a4908" />


Se creó una prueba de estrés que produce simultáneamente un gran número de partículas al presionar la tecla espacio, que genera cerca de 2000 partículas. Se seleccionó esta prueba para comprobar si el sistema es capaz de gestionar un elevado número de objetos dinámicos sin errores ni inconvenientes de memoria.

Con el fin de comprobar la dimensión del vector y analizar el rendimiento del sistema bajo alta carga, se puso un breakpoint mientras se actualizaba el vector de partículas.

En la captura se aprecia que el vector particles tiene más de 2000 elementos. A pesar de esta cantidad, el sistema sigue ejecutando los métodos `update()` y `draw()` sin fallos. Además, se puede ver que a medida que las partículas mueren, el tamaño del vector disminuye de manera gradual, lo cual evidencia que los objetos son eliminados correctamente.

Esta prueba confirma que el sistema gestiona correctamente una condición límite en la que se generan múltiples partículas al mismo tiempo. La aplicación sigue operando adecuadamente; las partículas se actualizan sin fallos y la memoria se va liberando a medida que las partículas van muriendo. Esto evidencia que la implementación es sólida y gestiona adecuadamente situaciones extremas.

</details>

## Bitácora de reflexión
