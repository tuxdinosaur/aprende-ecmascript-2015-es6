# Plantillas de string con funciones de etiquetado

Una forma avanzada de plantillas de texto son las plantillas de texto *etiquetadas*. Con ellas seremos capaces de modificar la salida de las plantillas de texto utilizando una función. Dicha función debe tener la siguiente firma:

```javascript
function tag(string, values) { }
```

No hace falta que la función se llame `tag`, podemos llamarla como queramos. Tan solo la he llamado así de forma ilustrativa.

El primer argumento contiene un array de literales y el segundo, cada uno de los valores de substitución. 

```javascript
// ES6
const tag = (strings, args) => {
  return strings.map(s => 
    s.split('').map(s => `${s}.`).join(''))
  .join('');
}

console.log(tag`Hello, World!`);
// "H.e.l.l.o.,. .W.o.r.l.d.!."
```

En el ejemplo anterior, hemos creado una función de etiquetado en la que "rompemos" el string en un array de caracteres, lo recorremos con una función `map` y añadimos un punto a cada carácter mediante un template string. Por último, lo unimos todo y el resultado es una función que te añade un punto a cada carácter del string facilitado.

Obviamente, este tipo de comportamiento no es demasiado útil, pero el límite del potencial que ofrecen las funciones de etiquetado está en nuestra imaginación.

Podríamos, por ejemplo pensar en cómo haríamos una función de internacionalización (i18n) de literales para nuestra aplicación. Con una función de etiquetado en todos nuestros strings, podríamos devolver en tiempo de ejecución el literal adecuado a la cultura del usuario.

Una aproximación muy sencilla podría ser la siguiente:

```javascript
// ES6
const dict = {
  es: {
    'days ago': 'hace %{count} días'
  },
  en: {
    'days ago': '%{count} days ago'
  }
};

const i18n = (strings, args) => {
  const key = strings.join('').trim();
  return dict[culture][key].replace('%{count}', args);
};

var culture = 'es';
console.log(i18n`${8} days ago`);

culture = 'en';
console.log(i18n`${8} days ago`);
```
    
Creamos un diccionario que contenga todas las traducciones de nuestra aplicación. En nuestro caso, tiene los idiomas inglés (en) y español (es). donde la clave es el literal a traducir y el valor es el literal traducido. Queremos traducir un literal que informa al usuario cuántos días han pasado desde que ocurrió un evento.

En este ejemplo, hemos añadido un "place holder", `${count}`, que nos servirá para sustituir el contador de días. Este elemento es totalmente arbitrario, no tiene nada que ver con "template strings": su nombre lo hemos decidido nosotros. Pero nos será útil porque en inglés el orden de las palabras es distinto que en castellano.

A continuación tenemos nuestra función de etiquetado: `i18n`. Fíjate que, llamándolo de esta manera, el cometido de la etiqueta en los "template strings" queda muy explícito. Lo único que hace esta función es asegurarse de que tiene una única clave, ya que recordemos que el primer argumento es un array de strings; y posteriormente buscar el string en el diccionario para devolver el traducido. No sin antes, reemplazar el elemento `%{count}` por el valor proporcionado.

El resultado, es el literal traducido en cada idioma. 

La variable culture simplemente ilustra cómo funcionaría la función cambiando de cultura, pero obviamente en una aplicación real habría que buscar una forma más elegante de obtenerla. Para trabajar con i18n, te recomiendo la librería de Airbnb [Polyglot](http://airbnb.io/polyglot.js/).