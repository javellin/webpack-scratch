# Webpack 4 project:

- VueJS;
- Babel loader;
- Jest.

Webpack scratch is a webpack 5 and vuejs front end application, built from scratch, wich will integrate with a NodeJS backend app in the near future.

We will use VueJS to write front end components, webpack as a bundler for our javascript code, babel for transpiling to ES6 and Jest for unit testing.

I didn't find any complete documentation with all this stuff together in my native language (pt-BR), so I decided to translate and adapt some things from a good written documentation I read, by Daniel Cook. 

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

Também precisaremos adicionar à seção scripts do nosso `package.json` nosso script de build: 

```json
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
    <script src="dist/index.js" type="text/javascript"></script>
  </body>
</html>
```

### Webpack-dev-server:

Vamos então configurar nosso webpack-dev-server, assim não precisaremos buildar o projeto toda vez que fizermos algum tipo de mudança. Instale a dependência:

```
$ npm install --save-dev webpack-dev-server
```

Adicione mais um script à seção de scripts do seu `package.json`: 

```json
"dev": "webpack-dev-server --config build/webpack.config.dev.js"
```

Agora quando rodarmos o comando:

```
$ npm run dev
```

Poderemos finalmente então, acessar nossa aplicação, navegue para: `http://localhost:8080` e lá está. Porém, tem um pequeno problema, se eu mudar meu conteúdo do html de `Hello World` para `Olá Mundo`, o navegador não irá atualizar sozinho.

Qual o problema? Quando criamos nosso `index.html`, nós "chumbamos" o caminho para o nosso javascript. Para essa atualização automática funcionar, precisamos permitir que esse caminho sejá injetável, e assim o dev-server poderá atualizar o html com as novas mudanças automagicamente.

Vá até seu index.html e remova a tag `<script>` que aponta para o `dist/index.js`.

```html
<script src="dist/index.js" type="text/javascript"></script>
```

Agora instale a dependência do `html-webpack-plugin`:

```
$ npm install --save-dev html-webpack-plugin
```

Precisamos adicionar isso ao nosso arquivo de configuração do webpack, que vai ficar mais ou menos assim: 

```javascript
'use strict'
const { VueLoaderPlugin } = require('vue-loader')
const HtmlWebpackPlugin = require('html-webpack-plugin')

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
    new VueLoaderPlugin(),
    new HtmlWebpackPlugin({
        filename: 'index.html',
        template: 'index.html',
        inject: true
    })
  ]
}
```

Agora se você rodar novamente a aplicação, verá que é possível mudar a mensagem no `App.vue` e a tela será imediatamente recarregada no browser. É tudo muito legal e divertido, mas ainda temos um problema.

Não queremos que a tela seja inteira recarregada sempre que fizermos uma mudança. Para corrigir isso, precisamos adicionar um pouco mais de configuração: 

```javascript
'use strict'

const webpack = require('webpack')
const { VueLoaderPlugin } = require('vue-loader')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  mode: 'development',
   devServer: {
    hot: true,
    watchOptions: {
      poll: true
    }
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        use: 'vue-loader'
      }
    ]
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new VueLoaderPlugin(),
    new HtmlWebpackPlugin({
        filename: 'index.html',
        template: 'index.html',
        inject: true
    })
  ]
}
```

Agora você verá que, apesar das atualizações que você fizer, a tela não será inteira recarregada.

## Estilo (CSS):

Com tudo isso funcionando, vamos adicionar um pouco de CSS e ver como ficará. Mude seu arquivo `App.vue`, para que ele fique parecido com isso: 

```
<template>
  <div class="full-width center-content">
    <h1>Hello World!</h1>
  </div>
</template>

<style scoped>
.full-width {
  width: 100%;
}
.center-content {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
```

A tela deveria carregar novamente as atualizações, mas neste caso você provavelmente vai se deparar com um erro no console dizendo que não foi possível lidar com aquele trecho CSS. Isso também aconteceria mesmo que fosse utilizado um arquivo de CSS separado. O probelma se dá ao fato de não conseguir tratar um arquivo CSS.

Para corrigir isso, precisamos configurar um loader para nossos amigos CSS, adicione isso à seção rules da sua configuração do webpack:

```
{
  test: /\.css$/,
  use: [
    'vue-style-loader',
    'css-loader'
  ]
}
```

E vamos instalar a dependência deste loader: 

```
$ npm install --save-dev vue-style-loader
```

Agora com certeza o CSS será carregado e você verá o estilo aplicado.

### Primeiro componente

Isso é apenas para um aprendizado básico, então não iremos nos aprofundar na questão de componentes aqui. Vamos criar algo bem básico, adicione um diretório `hello` e um arquivo `HelloComponent.vue` dentro dele. Este componente apenas receberá um nome como uma propriedade e o mostrará na tela.

```javascript
<template>
  <h1>Hello {{ name }}!</h1>
</template>

<script>
export default {
  props: {
    name: {
      type: String,
      required: true
    }
  }
}
</script>

<style lang="stylus" scoped>
h1
  color red
</style>
```

Agora mude o `App.vue`, para que ele possa usar nosso novo componente: 

```javascript
<template>
  <div class="full-width center-content">
    <hello-component name="World" />
  </div>
</template>

<script>
import HelloComponent from './components/hello/HelloComponent.vue'
export default {
  components: {
    HelloComponent
  }
}
</script>

<style scoped>
.full-width {
  width: 100%;
}
.center-content {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
```

Isso irá funcionar, mas browsers mais antigos vão ter problemas com essa sintaxe do ES6, então vamos instalar o Babel para transpilar nosso código para ES5 e compreenda uma maior quantidade de navegadores.

Primeiro, instale sua dependência: 

```
$ npm install --save-dev babel-core babel-loader babel-preset-env
```

Agora adicione o loader do babelo no seu arquivo de configuração do webpack, assim como foi feito com o CSS e o Vue:

```javascript
{
  test: /\.js$/,
  use: 'babel-loader'
}
```

Vale ressaltar que essa configuração do loader do babel venha apenas depois do loader do vue (vue-loader), pois o `vue-loader` divide o arquivo em diferentes módulos para o `html, js e o css`, e nós queremos que o `babel-loader` processe este `js`.

Finalmente criamos um arquivo na raiz do projeto com o nome `.babelrc`, será onde colocaremos as configurações específicas do `babel`. D e início, vamos deixar o mais simples possível: 

```json
{
  "presets": [
    ["env", {
      "modules": false,
      "targets": {
        "browsers": ["> 1%", "last 2 versions", "not ie <= 8"]
      }
    }]
  ]
}
```

Agora podemos rodar nossa aplicação novamente, não vemos nenhuma mudança visual, mas por debaixo do capô, nosso código ES6 está sendo transpilação para ES5.

### Recursos estáticos:

Recursos como imagens e vídeos não serão processados pelo Webpack, mas nós queremos que eles sejam copiados para nosso diretório `dist`, para que fiquem disponíveis ao construir a aplicação.

Para isso instalaremos a dependência do `copy-webpack-plugin`, que fará esse serviço para nós:

```
$ npm install --save-dev copy-webpack-plugin
```

Então nosso arquivo de configuração do webpack ficará mais ou menos assim: 

```javascript
'use strict'
const webpack = require('webpack')
const { VueLoaderPlugin } = require('vue-loader')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const CopyWebpackPlugin = require('copy-webpack-plugin')
const path = require('path')

function resolve (dir) {
  return path.join(__dirname, '..', dir)
}

module.exports = {
  mode: 'development',
  devServer: {
    hot: true,
    watchOptions: {
      poll: true
    }
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        use: 'vue-loader'
      },
      {
        test: /\.css$/,
        use: [
          'vue-style-loader',
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new VueLoaderPlugin(),
    new HtmlWebpackPlugin({
        filename: 'index.html',
        template: 'index.html',
        inject: true
    }),
    new CopyWebpackPlugin([{
      from: resolve('static/img'),
      to: resolve('dist/static/img'),
      toType: 'dir'
    }])
  ]
}
```

Agora todas imagens que colocarmos no nosso diretório `static/img` será copiado um diretório equivalente na nossa pasta `dist`.

### Teste unitário

Como parte do ecosistema Vue, `vue-test-utils` é a biblioteca oficial para testes unitários. Suporta vários `test runners` (por ex. karma e jest), renderização superficial, simulação de interação do usuário, mock para vuex e vue-router e atualizações síncronas.

Neste exemplo, iremos utilizar Jest para rodar os testes. Vamos instalá-lo:

```
$ npm install --save-dev jest babel-jest vue-jest jest-serializer-vue @vue/test-utils
```

Jest é o `test runner`, `vue-jest` é um pacore para transformar componentes Vue no formato correto para o Jest e `jest-serializer-vue` é um pacote que suporta tirar `snapshots` de componentes Vue. Teste utilizando `snapshot` permite renderizar um DOM completo e realizar comparações para garantir que não foi alterado.

Ao invés de uma configuração à parte, o Jest é configurado dentro do seu `package.json`. Adicione isso ao fim do arquivo:

```json
"jest": {
  "moduleFileExtensions": [
    "js",
    "vue"
  ],
  "moduleNameMapper": {
    "^@/(.*)$": "<rootDir>/src/$1"
  },
  "transform": {
    "^.+\\.js$": "<rootDir>/node_modules/babel-jest",
    ".*\\.(vue)$": "<rootDir>/node_modules/vue-jest"
  },
  "snapshotSerializers": [
    "<rootDir>/node_modules/jest-serializer-vue"
  ]
}
```

E também adicionaremos um novo script de execução no nosso `package.json`

```json
"test": "jest"
```

Precisamos agora configurar o babel para transpilar também os nosso testes, adicione isso ao `.babelrc`:

```json
"env": {
  "test": {
    "presets": [
      ["env", { "targets": { "node": "current" }}]
    ]
  }
}
```

Agora o Jest já está configurado e podemos realmente escrever um teste unitário. Mantendo essa estrutura simples, vamos apenas montar o nosso componente App e verificar que ele possui a classe `.center-content`. Jest por padrão vai procurar por testes em um diretório de nome `__tests__`, etnão vamos criar essa pasta logo abaixo do nosso `src`, e dentro dela, um arquivo `App.spec.js`.

```javascript
import {mount, createLocalVue} from '@vue/test-utils'
import App from '../App'

test('App has a .center-content class', () => {
    const vue = createLocalVue()

    const app = mount(App, {vue})

    expect(app.classes()).toContain('center-content')
})
```

Nós usamos o `vue-test-utils` para montar o nosso App e depois rodamos um simples `expect` para verificar a classe do componente. Para rodar o teste, execute:

```
$ npm run test
```

### FIM !

Mudanças irão acontecer, webpack ainda é algo bem novo, então conto com sua ajuda para manter essa documentação em português atualizada para futuras versões do webpack. Se chegou até aqui, muito obrigado pela paciência e divirta-se!

### THE END !

Changes and updates are always coming for webpack, it's a new thing yet.So I hope I can count on you to keep this updated in whatever language you want for future webpack updates. If you got until here, thanks for the patience and have fun!
