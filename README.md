# Webpack 4 project:

- VueJS;
- Babel loader;
- Jest.

Webpack scratch is a webpack 5 and vuejs front end application, built from scratch, wich will integrate with a NodeJS backend app in the near future.

We will use VueJS to write front end components, webpack as a bundler for our javascript code, babel for transpiling to ES6 and Jest for unit testing.

I didn't find any documentation in my native language (pt-BR), so I decided to translate and adapt some things from a good written documentation I read, by Daniel Cook. 

Here is the links:
- Part 1: https://itnext.io/vuejs-and-webpack-4-from-scratch-part-1-94c9c28a534a.
- Part 2: https://itnext.io/vue-js-and-webpack-4-from-scratch-part-2-5038cc9deffb.
- Part 3: https://itnext.io/vue-js-and-webpack-4-from-scratch-part-3-3f68d2a3c127.

## Passo a passo em português (pt_BR)

### Pré requisitos:

- Node
- Npm

### O início:

A primeira coisa a ser feita é criar um novo diretório com o nome que você quiser, entrar nele, e rodar o famoso comando:

```
$ npm init
```

Serão feitos alguns questionamentos com relação ao novo projeto criado, como nome, descrição, autor e etc. Podem ser utilizadas as opções default mesmo. Feito isso, teremos agora um arquivo package.json completamente limpo, hora de adicionar nossas bibliotecas. 

Vamos começar pelo Vue. Digite o seguinte comando  na raiz do seu diretório:

```
$ npm install --save vue vue-router
```

Agora vamos ao webpack:

```
$ npm install --save-dev webpack webpack-cli
```

Estes comandos nos farão ter o vue e o webpack instalados, respectivamente.

### Estrutura da aplicação:

Nós teremos todo o código da aplicação dentro de uma subpasta ```src```, nossos componentes, arquivos Vue e etc. Dentro dela iremos adicionar:

- ```index.js```: O ponto de entrada da aplicação;
- ```App.vue```: O componente raiz (Vue);
- ```pages```: O diretório onde estarão todas a telas do sistema (terá uma rota definida para cada uma dessas);
- ```components```: Um diretório contendo todos nossos componentes em bloco (serão organizados em subpastas);
- ```router```: Um diretório para toda nossa configuração de rotas do vue.

Nosso `index.js` terá o código mais simples possível por enquanto:

```javascript
import Vue from 'vue'
import App from './App.vue'
new Vue({
  el: '#app',
  render: h => h(App)
})
```

Isto irá basicamente renderizar nosso componente no DOM, e dar um id para ele, no caso, "#app".
Vamos colocar um trecho bem simples de código no nosso componente vue (App.vue), apenas para que possamos ver funcionando:

```html
<template>
  <div>
    <h1>Hello World!</h1>
  </div>
</template>
```

No vue, é padrão deixar html, script e estilo, todos dentro do componente. Assim teremos 3 tags: `template`, `script` e `style`. Agora utilizaremos por enquanto apenas a tag `template`, para definir nosso html.

### Webpack:

Agora que temos um pouco de código Vue, precisamos empacotá-lo, para que seja usado dentro do nosso html principal.

Webpack é um empacotador para aplicações Javascript, quando rodamos o comando `webpack`, pedimos que crie um ponto de entrada e construa um gráfico de dependência da nossa aplicação, puxando todas aquelas dependências para dentro de um ou mais pacotes que podem ser incluídos na aplicação. 

Ele suporta vários tipos diferentes de arquivos através de `loaders`, `loaders` vão pegar arquivos que não possuem o conceito de módulos (ex. css) e processá-los de uma forma que possam participar dessa dependência gráfica que o webpack está construindo.

Para ver mais sobre o webpack, clique aqui: https://webpack.js.org/.

Vamos começar com uma build simples do webpack. Na raiz do projeto, crie um diretório chamado `build` e dentro dele um arquivo chamado `webpack.config.dev.js`. Nós iremos usar isso para configurar um loader para nossos arquivos `.vue`.:

```javascript
'use strict'
const { VueLoaderPlugin } = require('vue-loader')
module.exports = {
  mode: 'development',
  module: {
    rules: [
      {
        test: /\.vue$/,
        use: 'vue-loader'
      }
    ]
  },
  plugins: [
    new VueLoaderPlugin()
  ]
}
```

Esta seção de módulos irá conter todos os nossos loaders, cada declaração de loader requer dois atributos básicos:

- `test`: Uma expressão regular utilizada para identificar os tipos de arquivos que serão processados pelo loader;
- `loader`: O nome do loader.

Para isso funcionar precisaremos instalar mais algumas dependências:

```
$ npm install --save-dev vue-loader vue-template-compiler vue-style-loader css-loader 
```

Também precisaremos adicionar à seção scripts do nosso package json nosso script de build: 

```javascript
"build": "webpack --config build/webpack.config.dev.js"
```

Agora vamos criar um `index.html` que irá puxar o nosso bundle. Ficará na raiz do projeto:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>My Vue app with webpack 4</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="dist/main.js" type="text/javascript"></script>
  </body>
</html>
```
