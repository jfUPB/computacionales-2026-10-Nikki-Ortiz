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

## Bitácora de reflexión
