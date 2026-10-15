# Taller 2 — Investigación: Teoría de React

**Nombre:** Oscar Jaime Buelvas Florez (Osky)
**Curso:** Desarrollo Front-End

---

## Punto 1 — ¿Qué es React?

React es una **librería de JavaScript** para construir interfaces de usuario a partir de
piezas reutilizables llamadas componentes. En vez de escribir cada pantalla como un
bloque único de HTML y JavaScript, con React divides la interfaz en pedazos pequeños
(un botón, una tarjeta, un formulario) y luego los combinas para armar páginas
completas.

La creó **Jordan Walke**, ingeniero de Facebook, quien construyó el primer prototipo
en 2011 (inspirado en una librería interna llamada XHP). Facebook lo liberó como
código abierto en **mayo de 2013**, durante la conferencia JSConf US.

**¿Librería o framework?** React es oficialmente una **librería**, no un framework, y
la diferencia importa: un framework (como Angular) te impone una estructura completa
—routing, manejo de formularios, peticiones HTTP, todo viene incluido y hace las cosas
"a su manera". Una librería, en cambio, resuelve un problema puntual —en este caso,
dibujar y actualizar la interfaz— y te deja elegir el resto de las piezas (para rutas
usas React Router, para peticiones usas fetch o Axios, etc.). Por eso se dice que React
es "no opinado": tú armas tu propio stack alrededor de él.

**¿Qué significa que sea declarativo?** Significa que tú describes *cómo se debe ver*
la interfaz para cada estado de los datos, y React se encarga de calcular qué cambió
en la pantalla y actualizarlo. No le dices paso a paso "busca este elemento, cámbiale
el texto, agrégale una clase" (eso sería el enfoque imperativo); simplemente declaras
"cuando el contador valga 5, este texto debe decir 5", y React hace el resto.

---

## Punto 2 — ¿Qué problema resuelve React?

Antes de React, construir una interfaz interactiva con JavaScript "a mano" significaba
manipular el DOM directamente: buscar un elemento con `document.getElementById`,
cambiarle el `innerHTML`, agregarle o quitarle clases, y repetir ese proceso cada vez
que cambiaba algo. En una aplicación pequeña esto es manejable, pero en una aplicación
grande —con decenas de elementos que dependen unos de otros— se vuelve un problema real:
es fácil que una parte de la pantalla quede desactualizada respecto a los datos, o que
dos fragmentos de código terminen pisándose al intentar actualizar el mismo elemento.

Esa es la diferencia entre el enfoque **imperativo** y el **declarativo**:

- **Imperativo:** describes el *cómo*, paso a paso ("selecciona este `div`, cámbiale
  el texto, luego agrégale esta clase").
- **Declarativo:** describes el *qué* ("este texto debe mostrar el nombre del
  usuario"), y la herramienta —React— decide cómo llegar ahí.

React mantiene la interfaz **sincronizada con los datos** (el *estado*) gracias a que,
cada vez que el estado cambia, React vuelve a calcular cómo debería verse la interfaz
en ese momento, compara ese resultado con lo que hay actualmente en pantalla (usando
una copia en memoria del DOM, el "Virtual DOM"), y solo actualiza en el navegador las
partes que realmente cambiaron. El desarrollador nunca tiene que acordarse de
actualizar manualmente cada elemento relacionado con un dato.

Ahí entran los **componentes reutilizables**: en vez de escribir la lógica de "cómo se
actualiza una tarjeta de producto" en veinte lugares distintos del código, la escribes
una sola vez dentro de un componente `TarjetaProducto`, y lo usas las veces que
necesites. Si el componente tiene un error o hay que mejorarlo, se corrige en un solo
lugar y el cambio se refleja en todas partes donde se usa.

---

## Punto 3 — Principales componentes de React

### 1. Componentes

Un componente es, en esencia, una función de JavaScript que recibe datos y devuelve
una descripción de lo que debe mostrarse en pantalla (escrita en JSX). Se crean como
funciones normales, con la convención de que su nombre empieza en mayúscula:

```jsx
function Saludo() {
  return <h1>Hola, soy Osky</h1>;
}
```

Características principales: son **reutilizables** (el mismo componente se puede usar
varias veces), **componibles** (un componente puede contener otros componentes
adentro) y **aislados** (cada uno maneja su propia lógica, sin interferir con los
demás).

### 2. JSX

JSX es una extensión de sintaxis que permite escribir código parecido a HTML dentro de
JavaScript. No es HTML real: por debajo, cada etiqueta JSX se traduce a una llamada de
función que crea un objeto describiendo ese elemento.

```jsx
const elemento = <p>Este texto viene de una variable: {nombre}</p>;
```

Reglas básicas: debe devolver un único elemento raíz (o usar un fragmento `<>...</>`
para agrupar varios sin agregar una etiqueta extra), las expresiones de JavaScript van
entre llaves `{}`, y los atributos usan `camelCase` (por ejemplo `className` en lugar
de `class`, porque `class` es palabra reservada en JavaScript).

### 3. Props

Las *props* (abreviación de "properties") son la forma en que un componente recibe
datos desde el componente que lo está usando. Funcionan parecido a los argumentos de
una función: son de solo lectura, el componente que las recibe no puede modificarlas.

```jsx
function Saludo(props) {
  return <h1>Hola, {props.nombre}</h1>;
}

// Uso:
<Saludo nombre="Osky" />
```

Sirven para que un mismo componente se comporte distinto según quién lo use, sin tener
que duplicar su código.

### 4. Estado (`useState`)

El estado es la información que un componente guarda y que puede cambiar con el
tiempo (por ejemplo, el valor de un contador, o si un menú está abierto o cerrado). El
hook `useState` permite declarar una variable de estado dentro de un componente de
función:

```jsx
import { useState } from "react";

function Contador() {
  const [cuenta, setCuenta] = useState(0);

  return (
    <button onClick={() => setCuenta(cuenta + 1)}>
      Has hecho clic {cuenta} veces
    </button>
  );
}
```

`useState(0)` devuelve un par: el valor actual (`cuenta`) y una función para
actualizarlo (`setCuenta`). Cada vez que se llama a `setCuenta`, React vuelve a
ejecutar el componente con el nuevo valor y actualiza en pantalla únicamente lo que
cambió — así es como la interfaz "reacciona" a los cambios de estado.

### 5. Eventos

React permite escuchar eventos del usuario (clics, escritura, envío de formularios)
pasando funciones directamente como atributos, en `camelCase`: `onClick`, `onChange`,
`onSubmit`, etc.

```jsx
function Campo() {
  const [texto, setTexto] = useState("");

  return (
    <input
      value={texto}
      onChange={(evento) => setTexto(evento.target.value)}
    />
  );
}
```

Aquí, cada vez que el usuario escribe algo en el input, se dispara `onChange`, que
actualiza el estado `texto` con lo que el usuario escribió, y React vuelve a mostrar
ese valor en el campo. Así es como React conecta la interacción del usuario con los
cambios de estado.

---

## Punto 4 — Importancia de React en la industria

Las empresas eligen React por varias razones que se refuerzan entre sí: su enfoque por
componentes permite que equipos grandes trabajen en paralelo sin pisarse; el Virtual
DOM hace que las actualizaciones de interfaces complejas sean eficientes; y al llevar
más de una década en producción, es una tecnología probada a gran escala (Meta la usa
en Facebook e Instagram desde hace años).

A su alrededor se formó un **ecosistema** enorme: empaquetadores y herramientas de
build, librerías de manejo de estado, y sobre todo *frameworks construidos encima de
React* como **Next.js**, que añade renderizado del lado del servidor, generación
estática de páginas y ruteo integrado. Desde la versión **React 19** (publicada en
diciembre de 2024) se sumó soporte oficial a los *React Server Components*, que
permiten obtener datos en el servidor antes de enviar la página al navegador,
mejorando el rendimiento inicial.

React lo sigue manteniendo **Meta**, en conjunto con una comunidad enorme de
desarrolladores y empresas externas que contribuyen código, reportan errores y crean
librerías complementarias (React Router, frameworks como Next.js, herramientas de
testing, etc.). Esa combinación de respaldo corporativo y comunidad abierta es parte de
por qué React se mantiene vigente: no depende de una sola empresa para evolucionar.

Para un desarrollador front-end, saber React es relevante porque sigue siendo una de
las tecnologías más pedidas en ofertas de empleo del área, y porque entender su modelo
por componentes y su manejo de estado ayuda incluso cuando se trabaja con otras
herramientas (Vue, Svelte, etc.), ya que varias adoptaron ideas similares.

---

## Punto 5 — Grandes empresas que utilizan React

**Netflix** reconstruyó la interfaz de su plataforma con React a partir de 2015. La
usan tanto en la web como en las aplicaciones de TV y escritorio, apoyándose en
renderizado del lado del servidor para que la interfaz cargue rápido incluso en
dispositivos con poca potencia (como los Smart TVs), algo clave para una plataforma de
streaming donde cada segundo de carga importa.
*(Fuente: Monocubed — "List of Top 12 Popular Websites Built With React"<br>https://www.monocubed.com/blog/websites-built-with-react/)*

**Instagram**, propiedad de Meta, está construido casi por completo con React. Lo usan
para manejar contenido dinámico (el feed, las historias, los comentarios en tiempo
real) sin tener que recargar la página, lo cual es esencial en una app donde el
contenido cambia constantemente mientras el usuario navega.
*(Fuente: EcuRed — "React js"<br>https://www.ecured.cu/React_js)*

Otras empresas que también usan React en sus productos, según las mismas fuentes,
incluyen Uber (en su app de viajes y en Uber Eats), Airbnb y la plataforma de
e-commerce de Nike.

---

## Fuentes consultadas

- Wikipedia — *React (software)*: https://en.wikipedia.org/wiki/React_(software)
- Documentación oficial de React: https://react.dev
- React Blog — *React 19 Upgrade Guide* (diciembre de 2024): https://react.dev/blog/2024/12/05/react-19
- DEV Community — Ragul, *React*: https://dev.to/ragul_kannadasan/react-8jk
- Monocubed — *List of Top 12 Popular Websites Built With React*: https://www.monocubed.com/blog/websites-built-with-react/
- EcuRed — *React js*: https://www.ecured.cu/React_js
