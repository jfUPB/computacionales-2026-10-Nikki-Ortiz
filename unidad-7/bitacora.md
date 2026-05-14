# Unidad 7

## Bitácora de proceso de aprendizaje

### ACTIVIDAD 01

### ACTIVIDAD 02

`.lib` → usado en tiempo de compilación para enlazar el programa.
`.dll` → usado en tiempo de ejecución para que el programa funcione.

**`opengl32.lib` (de Windows)**

Es una biblioteca de enlace incluida con Windows.
Permite enlazar contra la API base de OpenGL en Windows y utilizar las funciones básicas de OpenGL 1.1.

Aunque las funciones modernas son implementadas por los drivers de la GPU, esta librería sigue siendo necesaria porque funciona como el enlace inicial entre el proyecto y OpenGL.

**`GLFW`**

Biblioteca multiplataforma encargada de:

* crear ventanas
* manejar teclado y mouse
* administrar el contexto OpenGL

Requiere dos archivos:

* `glfw3.lib`: le indica al compilador dónde están las funciones de GLFW.
* `glfw3.dll`: contiene el código real que se ejecuta en tiempo de ejecución.

Sin GLFW no existiría una ventana ni un contexto donde OpenGL pueda renderizar gráficos.

**`GLAD`**

Es un cargador de funciones de OpenGL.

Las funciones modernas de OpenGL no están incluidas en `opengl32.lib`, sino dentro de los drivers de la tarjeta gráfica. GLAD se encarga de localizarlas mediante `wglGetProcAddress` y cargarlas para que puedan utilizarse desde el código.

En otras palabras, GLAD permite acceder a OpenGL moderno.

**`GLM` (opcional)**

Biblioteca matemática para gráficos.

Facilita operaciones relacionadas con:

* vectores
* matrices
* transformaciones
* rotaciones
* cámaras
* escalas

GLM solo consiste en archivos fuente (`.hpp`), por lo que no necesita `.lib` ni `.dll`.

---

**Conexión entre todos**

GLFW crea la ventana y el contexto.
`opengl32.lib` permite enlazar el proyecto con la API básica de OpenGL.
GLAD carga las funciones modernas desde los drivers de la GPU.
Los drivers de la GPU ejecutan realmente las instrucciones gráficas.
GLM ayuda a realizar cálculos matemáticos para transformaciones y animaciones.

---

Lo primero que entra en funcionamiento es GLFW. Su trabajo principal es crear la ventana del programa y preparar el contexto donde OpenGL va a trabajar. Además, también administra eventos como el teclado, el mouse y el cierre de la ventana. Sin GLFW no existiría una superficie donde OpenGL pudiera dibujar.

Después aparece `opengl32.lib`, que es una librería incluida en Windows. Esta librería funciona como el primer puente entre el proyecto y OpenGL, ya que durante la compilación Visual Studio necesita saber dónde se encuentran las funciones de OpenGL. El problema es que esta librería solo soporta funciones antiguas.

Para solucionar eso se utiliza `GLAD`. Las funciones modernas de OpenGL no están almacenadas directamente en Windows, sino dentro de los drivers de la tarjeta gráfica. GLAD busca esas funciones dentro de los drivers y las carga para que puedan usarse desde el programa.

En realidad, quienes realizan el trabajo gráfico son los drivers de la GPU, ya que ellos contienen la implementación real de OpenGL moderno y ejecutan las instrucciones gráficas sobre la tarjeta de video.

Por otro lado, `GLM` actúa como una biblioteca matemática especializada en gráficos. Aunque no es obligatoria para abrir una ventana o renderizar, facilita mucho el trabajo con vectores, matrices, movimientos, rotaciones, cámaras y transformaciones.

La cadena de funcionamiento sería:

primero `GLFW` crea la ventana y el contexto,
después `opengl32.lib` enlaza el proyecto con OpenGL básico,
luego `GLAD` carga las funciones modernas desde los drivers,
los drivers de la GPU ejecutan realmente las operaciones gráficas,
y finalmente `GLM` ayuda a realizar los cálculos matemáticos necesarios para transformar objetos.

---

### ACTIVIDAD 03

**¿Qué es un contexto OpenGL?**

Un contexto OpenGL es una estructura interna que almacena toda la información necesaria para que OpenGL funcione correctamente.

Dentro del contexto se encuentra:

* el estado actual de OpenGL
* shaders activos
* buffers y texturas
* matrices y configuraciones
* la conexión con la ventana
* la versión de OpenGL utilizada

Por ejemplo, un contexto puede estar configurado para usar OpenGL 4.6 Core.

---

**¿Qué es OpenGL?**

OpenGL es una API (*Application Programming Interface*).

Consiste en un conjunto de funciones que el programador utiliza para enviar instrucciones gráficas a la GPU.

OpenGL no dibuja directamente en pantalla. Lo que hace es traducir las instrucciones del programa en comandos que la GPU puede ejecutar.

---

**¿Qué hace la GPU?**

La GPU recibe y procesa los datos enviados desde OpenGL.

Entre sus funciones principales están:

* procesar vértices y texturas
* ejecutar shaders
* transformar datos en píxeles
* dibujar en el framebuffer

El framebuffer es una región de memoria de video donde se almacena la imagen final antes de mostrarse en pantalla.

En resumen:

* el programador escribe código en C++
* OpenGL convierte ese código en instrucciones gráficas
* la GPU ejecuta esas instrucciones de manera paralela
* el resultado final aparece renderizado en pantalla

---

**¿Qué es el viewport?**

El viewport define qué región del framebuffer se utilizará para renderizar la imagen.

Se mide en píxeles y normalmente coincide con el tamaño completo de la ventana o framebuffer.


---


| Concepto        | ¿Qué es?                                                    | ¿Por qué es importante?                                                  |
| --------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------ |
| GLFW            | Biblioteca utilizada para crear ventanas y manejar eventos. | Evita tener que programar código específico para cada sistema operativo. |
| Contexto OpenGL | Entorno donde OpenGL almacena su estado y recursos.         | Sin él, las funciones de OpenGL no pueden ejecutarse correctamente.      |
| Framebuffer     | Memoria donde OpenGL almacena cada cuadro renderizado.      | Es la imagen que finalmente termina mostrándose en pantalla.             |
| Viewport        | Región del framebuffer utilizada para dibujar.              | Determina el área visible donde se renderizan los gráficos.              |

---

**1. ¿Qué es el contexto OpenGL?**

El contexto OpenGL es el entorno interno donde OpenGL guarda toda la información necesaria para funcionar: configuraciones, shaders, buffers, colores y recursos gráficos. Puede entenderse como el espacio activo donde las instrucciones gráficas tienen efecto. Sin un contexto, las funciones de OpenGL no tendrían dónde ejecutarse.

---

**2. ¿Cuál es el rol de GLFW y qué ventaja tiene usarla?**

GLFW se encarga de crear la ventana, generar el contexto OpenGL y manejar eventos como teclado, mouse y cambios de tamaño de la pantalla. Su principal ventaja es que simplifica el desarrollo multiplataforma, evitando trabajar directamente con funciones específicas de Windows, Linux o macOS.

---

**3. ¿Por qué OpenGL necesita un contexto?**

OpenGL necesita un contexto porque debe saber en qué ventana trabajar, qué versión está utilizando y qué recursos gráficos están disponibles. Una forma sencilla de entenderlo es pensar que OpenGL sería el artista, mientras que el contexto sería el taller con todas las herramientas necesarias para poder trabajar.

---

**4. ¿Qué es el framebuffer y a qué recuerda de las primeras unidades?**

El framebuffer es una región de memoria donde la GPU almacena temporalmente los píxeles generados antes de mostrarlos en pantalla. Esto recuerda al manejo manual de memoria visto en assembly, donde era necesario reservar espacios específicos para almacenar información.

---

**5. ¿Qué relación hay entre viewport y framebuffer?**

El framebuffer representa toda la superficie de memoria disponible para dibujar, mientras que el viewport define qué parte de esa superficie será utilizada realmente para renderizar. Es similar a tener una hoja completa y seleccionar únicamente una región específica donde se quiere pintar.

---

**6. ¿Qué rol juegan los drivers de la GPU y la GPU?**

Los drivers contienen la implementación real de muchas funciones modernas de OpenGL. Desde C++, OpenGL solamente envía instrucciones, pero quien realiza el procesamiento gráfico es la GPU mediante esos drivers. En otras palabras, OpenGL define qué hacer y la GPU ejecuta el trabajo pesado.

---

**7. ¿Por qué activar VSync?**

VSync sincroniza la actualización de la imagen con la frecuencia del monitor. Esto evita el efecto conocido como *tearing*, donde fragmentos de distintos cuadros aparecen mezclados en pantalla. En escenas estáticas puede pasar desapercibido, pero durante animaciones o movimientos rápidos mejora notablemente la fluidez visual.

---

**8. ¿Qué es OpenGL Legacy y qué diferencia tiene con OpenGL moderno?**

OpenGL Legacy corresponde al modelo antiguo de OpenGL, donde gran parte del pipeline estaba predefinido mediante funciones como `glBegin()` y `glEnd()`.

En OpenGL moderno el programador debe manejar explícitamente shaders, buffers y estados gráficos. Esto hace el sistema más complejo, pero también mucho más flexible y eficiente.

---

**9. ¿Qué es el shader program y por qué es importante?**

El shader program es el programa que se ejecuta dentro de la GPU para procesar la información gráfica. Generalmente está compuesto por un vertex shader y un fragment shader.

Es importante porque en OpenGL moderno no se puede renderizar nada sin indicar cómo deben transformarse los vértices y cómo deben colorearse los fragmentos.

---

**10. ¿Qué creo que hacen `setupTriangle()`, VAO y VBO?**

`setupTriangle()` probablemente prepara los datos del triángulo y los envía a la GPU. Dentro de esta función normalmente se crea un VBO (*Vertex Buffer Object*), encargado de almacenar los vértices en memoria gráfica, y un VAO (*Vertex Array Object*), que guarda la configuración necesaria para interpretar esos datos correctamente al momento de dibujar.

---

**11. Uso repetido del shader program y del VAO dentro del game loop**

Si el programa únicamente dibuja un solo objeto y nunca cambia el shader ni el VAO, no sería estrictamente necesario volver a activarlos en cada iteración del game loop, ya que OpenGL conserva el último estado activo hasta que otro lo modifique.

Sin embargo, en aplicaciones más grandes donde se renderizan múltiples objetos con distintos shaders o configuraciones, sí resulta necesario volver a enlazarlos constantemente.

---

**12. ¿Por qué es importante `glfwSwapBuffers()`?**

Porque OpenGL no renderiza directamente sobre la imagen visible, sino sobre un buffer trasero. `glfwSwapBuffers()` intercambia ese buffer oculto con el buffer visible en pantalla.

Si no se ejecutara esta función, la GPU seguiría renderizando internamente, pero los cambios nunca aparecerían visualmente o la ventana se quedaría congelada y con parpadeos.

---


### ACTIVIDAD 04

### ACTIVIDAD 05

<details>
<summary>Codigo</summary>

``` cpp
#include <iostream>
#include <glad/glad.h>
#include <GLFW/glfw3.h>


// Callback: ajusta el viewport cuando cambie el tamaño de la ventana
void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
	glViewport(0, 0, width, height);
}

// Procesa entrada simple: cierra con ESC
void processInput(GLFWwindow* window) {
	if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
		glfwSetWindowShouldClose(window, true);
}

// Tamaño de las ventanas
const unsigned int SCR_WIDTH = 400;
const unsigned int SCR_HEIGHT = 400;

// Fuentes de los shaders
const char* vertexShaderSrc = R"glsl(
    #version 460 core

    layout(location = 0) in vec3 aPos;

    // Uniform enviado desde C++ para mover el triángulo
    uniform vec2 offset;

    void main() {
        vec3 newPos = aPos;

        // Se modifica la posición del triángulo usando el mouse
        newPos.x += offset.x;
        newPos.y += offset.y;

        gl_Position = vec4(newPos, 1.0);
    }
)glsl";

const char* fragmentShaderSrc = R"glsl(
    #version 460 core

    out vec4 FragColor;

    // Uniform enviado desde C++ para cambiar el color
    uniform vec4 ourColor;

    void main() {
        FragColor = ourColor;
    }
)glsl";

// IDs globales
unsigned int VAO, VBO;
unsigned int shaderProg;

// Compila y linkea un programa de shaders, retorna su ID
unsigned int buildShaderProgram() {
	int success;
	char log[512];

	unsigned int vs = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vs, 1, &vertexShaderSrc, nullptr);
	glCompileShader(vs);
	glGetShaderiv(vs, GL_COMPILE_STATUS, &success);
	if (!success) {
		glGetShaderInfoLog(vs, 512, nullptr, log);
		std::cerr << "ERROR VERTEX SHADER:\n" << log << "\n";
	}

	unsigned int fs = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(fs, 1, &fragmentShaderSrc, nullptr);
	glCompileShader(fs);
	glGetShaderiv(fs, GL_COMPILE_STATUS, &success);
	if (!success) {
		glGetShaderInfoLog(fs, 512, nullptr, log);
		std::cerr << "ERROR FRAGMENT SHADER:\n" << log << "\n";
	}

	unsigned int prog = glCreateProgram();
	glAttachShader(prog, vs);
	glAttachShader(prog, fs);
	glLinkProgram(prog);
	glGetProgramiv(prog, GL_LINK_STATUS, &success);
	if (!success) {
		glGetProgramInfoLog(prog, 512, nullptr, log);
		std::cerr << "ERROR LINKING PROGRAM:\n" << log << "\n";
	}

	glDeleteShader(vs);
	glDeleteShader(fs);
	return prog;
}

// Crea un VAO/VBO con los datos de un triángulo
void setupTriangle() {
	float vertices[] = {
		-0.5f, -0.5f, 0.0f,
		 0.5f, -0.5f, 0.0f,
		 0.0f,  0.5f, 0.0f
	};

	glGenVertexArrays(1, &VAO);
	glGenBuffers(1, &VBO);

	glBindVertexArray(VAO);
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);
	glBindVertexArray(0);
}


int main()
{
	// 1) Inicializar GLFW
	if (!glfwInit()) {
		std::cerr << "Fallo al inicializar GLFW\n";
		return -1;
	}

	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 6);
	glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

	// 2) Crear ventana
	GLFWwindow* mainWindow = glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, "Ventana", nullptr, nullptr);
	if (!mainWindow) {
		std::cerr << "Error creando ventana1\n";
		glfwTerminate();
		return -1;
	}

	// 3) Lee el tamaño del framebuffer
	int bufferWidth, bufferHeight;
	glfwGetFramebufferSize(mainWindow, &bufferWidth, &bufferHeight);

	// 4) Callbacks 
	glfwSetFramebufferSizeCallback(mainWindow, framebuffer_size_callback);

	// 5) Cargar GLAD y recursos en contexto de window1
	glfwMakeContextCurrent(mainWindow);

	if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
		std::cerr << "Fallo al cargar GLAD (contexto1)\n";
		return -1;
	}

	// 6) Habilita el V-Sync
	glfwSwapInterval(1);

	// 7) Compila y linkea shaders
	shaderProg = buildShaderProgram();

	// 8) Genera el contenido a mostrar
	setupTriangle();

	// 9) Configura el viewport
	glViewport(0, 0, bufferWidth, bufferHeight);

	// Activamos el shader program para poder buscar los uniforms
	glUseProgram(shaderProg);

	// Obtenemos la ubicación de los uniforms dentro del shader
	int offsetLocation = glGetUniformLocation(shaderProg, "offset");
	int colorLocation = glGetUniformLocation(shaderProg, "ourColor");

	// 10) Loop principal
	while (!glfwWindowShouldClose(mainWindow))
	{
		// 11) Manejo de eventos
		glfwPollEvents();

		// 12) Procesa la entrada
		processInput(mainWindow);

		// 13) Configura el color de fondo y limpia el framebuffer
		glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT);

		// 14) Indica a OpenGL que use el shader program
		glUseProgram(shaderProg);

		// Dibuja el triángulo
		double xpos, ypos;
		glfwGetCursorPos(mainWindow, &xpos, &ypos);

		// Normalizo las coordenadas del mouse
		float x = (float)xpos / (float)SCR_WIDTH;
		if (x < 0) x = 0;
		if (x > 1) x = 1;

		float y = (float)ypos / (float)SCR_HEIGHT;
		if (y < 0) y = 0;
		if (y > 1) y = 1;

		// Envio el color y la posición del triángulo
		glUniform4f(colorLocation, x, y, 0.0f, 1.0f);

		// Envio el offset del triángulo normalizado a NDC
		glUniform2f(offsetLocation, x * 2 - 1, 1 - y * 2);

		// 15) Activa el VAO y dibuja el triángulo
		glBindVertexArray(VAO);
		glDrawArrays(GL_TRIANGLES, 0, 3);

		// 16) Intercambia buffers y muestra el contenido
		glfwSwapBuffers(mainWindow);
	}

	// 17) Limpieza
	glfwMakeContextCurrent(mainWindow);
	glDeleteVertexArrays(1, &VAO);
	glDeleteBuffers(1, &VBO);
	glDeleteProgram(shaderProg);

	glfwDestroyWindow(mainWindow);
	glfwTerminate();

	return 0;
}
```
</details>

---

<img width="402" height="432" alt="image" src="https://github.com/user-attachments/assets/930e6193-0620-433f-9507-1efc0d5a80a9" />

**Normalización de las coordenadas del mouse**

La función `glfwGetCursorPos()` devuelve la posición del mouse en píxeles dentro de la ventana. Como OpenGL no trabaja directamente con coordenadas en píxeles, fue necesario transformar esos valores a un rango entre 0 y 1 dividiendo entre el ancho y el alto de la ventana.

```cpp id="wzkbln"
float x = (float)xpos / (float)SCR_WIDTH;
float y = (float)ypos / (float)SCR_HEIGHT;
```

De esta forma se obtiene una posición relativa del cursor independiente del tamaño de la ventana. Además, estos valores normalizados también pueden utilizarse para controlar colores, ya que OpenGL maneja los componentes RGB dentro del rango de 0 a 1.

---

**Normalización a coordenadas NDC**

OpenGL posiciona los objetos usando coordenadas NDC (*Normalized Device Coordinates*), donde el espacio visible va desde -1 hasta 1 en ambos ejes. Por esta razón fue necesario convertir los valores normalizados anteriores mediante:

```cpp id="4e0lru"
glUniform2f(offsetLocation, x * 2 - 1, 1 - y * 2);
```

La expresión `x * 2 - 1` transforma el eje horizontal del rango `0..1` al rango `-1..1`. Por otro lado, `1 - y * 2` realiza la misma conversión en el eje vertical, pero además invierte la dirección del eje Y, ya que en las ventanas el eje vertical crece hacia abajo, mientras que en OpenGL crece hacia arriba.


## Bitácora de aplicación 

### Actividad 06

**EVIDENCIA 01 - Contexto y carga de OpenGL**

<img width="1918" height="793" alt="imagen" src="https://github.com/user-attachments/assets/8dddfa24-a437-432d-93a5-dfd2b9ad6e4a" />

La ejecución se detuvo justo antes de inicializar GLAD, en un momento donde GLFW ya había creado correctamente la ventana y el contexto OpenGL.

**Explicación**

En este punto puede verificarse que `mainWindow` ya fue creado y que `glfwMakeContextCurrent(mainWindow)` ya dejó activo el contexto OpenGL. Esto resulta fundamental porque GLAD necesita un contexto válido para poder comunicarse con los drivers de la GPU y obtener las funciones modernas de OpenGL.

**Justificación**

GLFW se inicializa primero porque es el encargado de construir el entorno gráfico básico. Después aparece GLAD, ya que depende de ese contexto previamente creado para localizar las direcciones reales de las funciones OpenGL. Si GLAD intentara cargarse antes de que existiera un contexto activo, la inicialización no podría realizarse correctamente y fallaría.

---

**EVIDENCIA 02 - Del arreglo al shader**

<img width="1876" height="990" alt="imagen" src="https://github.com/user-attachments/assets/64963eb7-f9a2-41be-b641-48e4450a2666" />
<img width="1915" height="1059" alt="imagen" src="https://github.com/user-attachments/assets/c2d441fb-39e3-40e8-8f55-3bf9ee140ab5" />

La ejecución se detuvo dentro de `setupTriangle()`, justo en el momento en que el arreglo `vertices` es enviado al VBO y OpenGL recibe la configuración necesaria para interpretar esos datos.

**Explicación**

El arreglo `vertices` almacena en la CPU las posiciones de los tres vértices del triángulo. Mediante `glBufferData()`, esos nueve valores flotantes se copian a la memoria de la GPU dentro del VBO. Luego, la instrucción `glVertexAttribPointer(0, 3, GL_FLOAT...)` le indica a OpenGL que debe leer esos datos agrupándolos de tres en tres y asociarlos con el atributo de entrada ubicado en el `location 0` del vertex shader.

**Justificación**

Esto evidencia que el atributo `aPos` del shader no obtiene información automáticamente, sino que recibe exactamente los datos que originalmente estaban en el arreglo del programa y que posteriormente fueron transferidos al pipeline gráfico a través del VBO y el VAO.

---

**EVIDENCIA 03 - Uniform y cambio visual**

<img width="1902" height="967" alt="imagen" src="https://github.com/user-attachments/assets/fc77f01d-085b-4f35-917f-6dc9f57eea1d" />

<img width="1899" height="699" alt="imagen" src="https://github.com/user-attachments/assets/b807027b-7098-491b-acca-80f9190629d8" />

**Explicación**

En este punto se puede comprobar que los vértices almacenados dentro del VBO permanecen exactamente iguales. Sin embargo, antes de renderizar se envían nuevos valores al shader utilizando uniforms. El uniform `offset` se encarga de desplazar todo el triángulo, mientras que el uniform `ourColor` modifica el color con el que se dibuja.

**Justificación**

Esto demuestra que el pipeline gráfico permite modificar el resultado visual sin necesidad de cambiar la geometría original. Los uniforms funcionan como parámetros dinámicos externos que la GPU utiliza durante la ejecución de los shaders para alterar propiedades como posición, color u otras transformaciones.

---

**EVIDENCIA 04 - Prueba de borde**

**PASO 1:**

Tomamos esta línea de  código que se mostró en la evidencia anterior 

`glUniform2f(offsetLocation, x * 2 - 1, 1 - y * 2);`

**PASO 2**

Le cambiamos los parámetros por:

`glUniform2f(offsetLocation, 1.0f,0.0f);`

**RESULTADO:**

<img width="400" height="442" alt="imagen" src="https://github.com/user-attachments/assets/a76a3d15-4c14-413a-a921-9de0df409926" />

Se esperaba que el triángulo se desplazara hacia la derecha hasta alcanzar el límite del espacio visible en OpenGL. Al ejecutar el programa, efectivamente el triángulo se movió hacia el borde derecho y parte de él quedó fuera de la pantalla.

A partir de esto se concluyó que el VBO nunca fue modificado, ya que los vértices originales permanecieron exactamente iguales. El cambio ocurrió únicamente en el uniform `offset`, el cual alteró la posición final del triángulo durante la ejecución del shader.

---

**EVIDENCIA 05 - Responsabilidad del pipeline**

Decidí enviar la posición del mouse como un `uniform` porque ese valor afecta al triángulo completo y no a cada vértice de manera individual. Si se hubiera utilizado un atributo, habría sido necesario repetir el mismo dato para los tres vértices dentro del VBO.

En cambio, el uso de un `uniform` permite conservar la geometría original sin cambios y modificar su comportamiento dinámicamente desde el shader en cada *draw call*. De esta manera se mantiene una separación clara entre los datos estáticos de la geometría y los datos dinámicos utilizados durante el renderizado dentro del pipeline gráfico.


## Bitácora de reflexión
