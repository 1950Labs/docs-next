# Enlace Clases y Estilos

Una necesidad común de _data binding_ es manipular la lista de clases de un elemento y sus estilos en línea. Como ambos son atributos, podemos usar `v-bind` para manejarlos: solo necesitamos crear una cadena de texto con nuestras expresiones. Sin embargo, concatenar cadenas de texto puede llegar a ser incómodo y propenso a errores. Por esta razón, Vue proporciona mejoras cuando se utiliza `v-bind` con `class` y `style`. Además de las cadenas de texto, las expresiones también pueden evaluar objetos o matrices.

## Enlace de clases HTML

### Sintaxis de Objeto

Podemos pasar un objeto a `:class` (versión abreviada de `v-bind:class`) para la asignación dinámica de clases:

```html
<div :class="{ active: isActive }"></div>
```

La sintaxis de arriba tiene como resultado la clase `active` según el [valor booleano](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) de la propiedad `isActive`.

Puede hacer múltiple asignación de clases al tener más campos en el objeto. Además, la directiva `:class` también puede ser utilizada con el atributo `class`. De modo que con la siguiente plantilla:

```html
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
```

Y el siguiente objeto de datos:

```js
data() {
  return {
    isActive: true,
    hasError: false
  }
}
```

Se renderizará:

```html
<div class="static active"></div>
```

Cuando `isActive` o `hasError` cambien, la lista de clases se actualizará consecuentemente. Por ejemplo, si `hasError` se convierte en `true`, la lista de clases se convertirá en `"static active text-danger"`.

El objeto no tiene por qué estar en la misma línea:

```html
<div :class="classObject"></div>
```

```js
data() {
  return {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
}
```

Esto renderizará el mismo resultado. También podemos enlazar una [propiedad computada](computed.md) que retorna un objeto. Esto es un patrón común y poderoso:

```html
<div :class="classObject"></div>
```

```js
data() {
  return {
    isActive: true,
    error: null
  }
},
computed: {
  classObject() {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### Sintaxis de Arreglo

Podemos pasar un arreglo a `:class` para aplicar una lista de clases:

```html
<div :class="[activeClass, errorClass]"></div>
```

```js
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```

Que renderizará:

```html
<div class="active text-danger"></div>
```

Si usted quisiera activar una clase en la lista de manera condicional, usted puede hacerlo con una expresión ternaria:

```html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

Esto siempre aplicará `errorClass`, pero solo aplicará `activeClass` cuando `isActive` sea verdadero.

Sin embargo, esto puede ser un poco verborrágico si usted tiene múltiples clases condicionales. Por eso también es posible utilizar la sintaxis de objeto dentro de la sintaxis de colección:

```html
<div :class="[{ active: isActive }, errorClass]"></div>
```

### Con Componentes

> Esta sección asume que usted tiene conocimientos sobre [Componentes de Vue](component-basics.md). Siéntase libre de saltársela y volver más tarde.

Cuando usa el atributo `class` en un componente personalizado con un único elemento raíz, estas clases se agregarán a este elemento. Las clases existentes en este elemento no serán sobrescritas.

Por ejemplo, si declara este componente:

```js
const app = Vue.createApp({})

app.component('my-component', {
  template: `<p class="foo bar">Hi!</p>`
})
```

Y agrega algunas clases al usarlo:

```html
<div id="app">
  <my-component class="baz boo"></my-component>
</div>
```

El HTML renderizado será:

```html
<p class="foo bar baz boo">Hi</p>
```

Lo mismo aplica para el enlace de clases:

```html
<my-component :class="{ active: isActive }"></my-component>
```

Cuando `isActive` es verdadero, el HTML renderizado será:

```html
<p class="foo bar active">Hi</p>
```

Si su componente tiene múltiples elementos raíz, necesitará definir cuál componente recibirá esta clase. Puede hacer esto utilizando la propiedad de componente `$attrs`:

```html
<div id="app">
  <my-component class="baz"></my-component>
</div>
```

```js
const app = Vue.createApp({})

app.component('my-component', {
  template: `
    <p :class="$attrs.class">Hi!</p>
    <span>This is a child component</span>
  `
})
```

Puede conocer más sobre herencia de atributos de componentes en la sección [atributos que no son propiedades](component-attrs.html).

## Enlace de estilos en línea

### Sintaxis de Objeto

La sintaxis de objeto para `:style` es muy sencilla, es similar a CSS, excepto que es un objeto JavaScript. Puede usar tanto _camelCase_ como _kebab-case_ (use comillas con _kebab-case_) para los nombres de propiedades CSS:

```html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

A menudo es una buena idea enlazar directamente un objeto de estilo para que la plantilla sea más limpia:

```html
<div :style="styleObject"></div>
```

```js
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

Nuevamente, la sintaxis de objeto es a menudo usada en conjunción con propiedades computadas que retornan objetos.

### Sintaxis de Arreglo

La sintaxis de arreglo `:style` permite aplicar múltiples objetos de estilo al mismo elemento:

```html
<div :style="[baseStyles, overridingStyles]"></div>
```

### Auto-prefijado

Cuando utilice una propiedad CSS que requiera [prefijos de proveedores](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) en `:style`,  como por ejemplo `transform`, Vue detectará automáticamente y agregará los prefijos correspondientes a los estilos aplicados.

### Valores múltiples

Puede proveer un arreglo de valores múltiples (de prefijos) a una propiedad de estilo, por ejemplo:

```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Esto solo renderizará el último valor en el array que el navegador soporte. En este ejemplo, se renderizará `display: flex` para los navegadores que soportan la versión sin prefijo de _flexbox_.
