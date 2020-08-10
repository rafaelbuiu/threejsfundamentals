Title: Three.js Fundamentos
Description: Your first Three.js lesson starting with the fundamentals
TOC: Comece aqui

Este é o primeiro de vários artigos sobre three.js.
[Three.js](https://threejs.org) é uma biblitoteca Javascript que facilita o uso de conteúdo 3D em uma página da web.


Three.js é frquentemente confundido com WebGL desde que muitas vezes, mas nem sempre, three.js utiliza WebGL para desenhar 3D.
[WebGL é um sistema básico que apenas desenha pontos, linhas e triângulos](https://webglfundamentals.org).

Para fazer alguma coisa útil com WebGL é necessário uma 
quantidade considerável de código e aqui é onde o three.js entra em ação. Ele manipula uma série
de fatores como cenas, luzes, sombras, materiais, texturas, equações matématicas e todas
as coisas que você teria que manipular manualmente caso estivesse usando puramente o WebGL.

Estes tutoriais supõem que você tenha conhecimento em Javascript que 
pela sua maior parte utiliza estilo ES6. [Veja aqui uma
lista de coisas que você deve saber](threejs-prerequisites.html).

A maioria dos browsers que suportam three.js são atualizados automaticamente
e por isso a maioria dos usuários estarão aptos a executar estes códigos.
Caso você queira fazer com que estes códigos sejam executados por browsers mais antigos,
dê uma olhada sobre um compilador como [Babel](https://babel.io).
É claro que os usuários que executam navegadores realmente antigos provavelmente têm máquinas
que não podem executar three.js.

A primeira lição, quando aprendemos a uma linguagem de programação, 
é fazer com que o computador imprima o texto "Hello World!". Para o 3D, a primeira lição
será a criação de um cubo. Então vamos fazer nosso "Hello cube!".

Antes de começarmos, observe abaixo como é a estrutura de uma aplicação three.js.
É necessário que alguns objetos sejam criados e que eles sejam conectados entre eles. 
Veja abaixo o diagrama de uma pequena aplicação three.js

<div class="threejs_center"><img src="resources/images/threejs-structure.svg" style="width: 768px;"></div>

Coisas a se notar no diagrama acima:

*Aviso: os termos abaixo estarão em inglês para que o uso futuro do aprendizado não seja afetado pela tradução.*

* Há um `Renderer` (Renderizador). Este é sem dúvida o principal objeto do three.js. Os objetos
  `Scene` (Cena) e `Camera` deverão ser passados para o `Renderer` que no caso renderizará( desenhará) a porção da cena 3D
  que está na parte visível do *`Frustum`* (Campo de visão) da camera como uma simples images 2D no elemento canvas do HTML.

* Este diagrama é um [scenegraph](threejs-scenegraph.html) (Gráfico de cena) que se caracteriza por ser uma estrutura do tipo árvore,
  que se consiste na lista dos objetos presentes na aplicação. Podemos ver objetos dos tipo `Scene` (Cena), 
  `Mesh`,  `Light`, `Group`, `Object3D` e `Camera`. 
  Um objeto do tipo `Scene` (Cena) é definido como a raiz do gráfico de cena e possui propriedades como
  cor de fundo e `Fog` (névoa). 
  
  Estes objetos definem a árvore hierarquica da aplicação 
  e representam onde e como os objetos serão apresentados.
  Objetos descendentes são posicionados e orientados relativamente ao seus antescendentes.
  Um exemplo claro seriam as rodas de um carro, nas quais são descendentes do objeto *Carro*. 
  A movimentação e orientação do carro automaticamente moverá e orientará o objeto *Roda*
  Encontre mais informações no [artigo sobre Gráficos de cena](threejs-scenegraph.html).

  Note que no diagrama, o objeto `Camera` está parcialmete dentro da estrutura do gráfico de cena. 
  Isto é para demonstrar que no three.js, a `Camera` não precisa pertencer diretamente à cena para funcionar.
  Como qualuqer outro objeto, a `Camera`, pode ser um objeto descendente de outro objeto e será movida e orientada relativamente ao objeto a que pertence.
  Há um ótimo exemplo de múltiplos objetos de `Camera` em um único gráfico de cena no final do [artigo sobre Gráficos de cena](threejs-scenegraph.html).

* Os objetos `Mesh` representam o desenho de uma `Geometry` específica com um
   `Material`. Os objetos `Material` e objetos `Geometry` podem ser usados por
    vários objetos `Mesh`. Por exemplo, para desenhar 2 cubos azuis em diferentes
    locais, precisariamos de 2 objetos `Mesh` para representar a posição e
    orientação de cada cubo, porém precisaríamos de apenas 1 objeto `Geometry` representando um cubo
    e apenas 1 objeto `Material` para especificar a cor azul. Ambos os objetos `Mesh` poderiam referenciar ao mesmo objeto `Geometry` e o
    mesmo objeto `Material`.

* Os objetos `Geometry` representam os dados das vértices de alguma parte da geometria como
   uma esfera, cubo, plano, cachorro, gato, humano, árvore, prédio, etc...
   Three.js oferece várias formas de  contruir 
   [geometrias primitivas](threejs-primitives.html). Você também pode
   [criar geometrias customizadas](threejs-custom-geometry.html) também como
   [carregá-las de um arquivo externo](threejs-load-obj.html).

* Os objetos do tipo `Material` representam
  [as propriedades da superfície que desenham a geometria](threejs-materials.html).
  Cisas como cores a serem usdas ou como brilhante a geometria deve ser. Um `Material` pode também
  referenciar à um ou mais objetos do tipo `Texture`  qe podem ser usados, por exemplo,
  para revertir um objeto com uma imagem específica.

* Os objetos do tipo `Texture`  geralmente representam imagens  [carregadas externamente](threejs-textures.html),
  [geradas atrvés de um elemento canvas](threejs-canvas-textures.html) or [ou a renderização de outra cena na sua aplicação](threejs-rendertargets.html).

* Objetos do tipo `Light` representam [diferentes tipos de luzes](threejs-lights.html) presentes na sua cena.

Agora que você já tem todas essas informções vamos criar um pequeno *"Hello Cube"* exemplo
que deve parecer com o seguinte:

<div class="threejs_center"><img src="resources/images/threejs-1cube-no-light-scene.svg" style="width: 500px;"></div>

Primeiro precisamos carregar o three.js na nossa aplicação

```html
<script type="module">
import * as THREE from './resources/threejs/r113/build/three.module.js';
</script>
```

É importante que você coloque `type="module"` na tag do script. Isso permite
usar a palavra-chave `import` para carregar o three.js. Existem outras maneiras
para carregar three.js, mas a partir da build r106, usar módulos é a maneira recomendada.
Os módulos têm a vantagem de facilmente importar outors módulos necessários para a execução 
do exemplo. Isso nos livra de ter que carregar outras dependências manualmente.

Em seguida, precisamos de uma tag `<canvas>`
```html
<body>
  <canvas id="c"></canvas>
</body>
```

Pediremos ao three.js para desenhar nessa tela, portanto, precisamos procurá-lo.

```html
<script type="module">
import * as THREE from './resources/threejs/r113/build/three.module.js';

+function main() {
+  const canvas = document.querySelector('#c');
+  const renderer = new THREE.WebGLRenderer({canvas});
+  ...
</script>
```

Após criarmos o elemnto `<canvas>`, precisamos criar um `WebGLRenderer`. O renderizador
é na verdade o responsável por pegar todos os dados que você forneceu
e executá-los na tela. No passado, haviam outros renderizadores
como `CSSRenderer`, ` CanvasRenderer` e provavelmente no futuro pode haver um
`WebGL2Renderer` ou` WebGPURenderer`. Por enquanto existe o `WebGLRenderer`
que usa WebGL para renderizar 3D na tela.

Observe que existem alguns detalhes esotéricos aqui. Se você não passar um elemento `<canvas>`
para o three.js, ele irá criar um para você, mas então você tem que adicioná-lo
ao seu documento usando appendChild em um elemento, ou até mesmo no corpo do arquivo.
Onde adicionar o `<canvas>` pode mudar dependendo do seu caso de uso
e você terá que alterar seu código. Po isso, passar uma tela
para three.js parece um pouco mais flexível. Podemos posicionar o `<canvas>` em qualquer lugar
e o código irá encontrá-lo no documento onde quer que seja sem a necessidade de 
alterar esse código se precisar alterá-lo.

Em seguida, precisamos de uma câmera. Vamos criar uma `PerspectiveCamera`.

```js
const fov = 75;
const aspect = 2;  // the canvas default
const near = 0.1;
const far = 5;
const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
```

`fov` é a abreviação de `field of view` (campo de visão, em inglês). Neste caso, 75 graus na dimensão vertical.
Observe que a maioria dos ângulos em three.js estão em radianos, mas por algum
motivo a `PerspectiveCamera` recebe essa informação em graus.

`aspect` é o aspecto de exibição da tela. Vamos repassar os detalhes
[em outro artigo](threejs-responsive.html), mas por padrão o elemento `<canvas>` é
300 x 150 pixels, o que torna o aspecto 300/150 ou 2.

`near` e` far` representam o espaço que será processado na frente da câmera.
Qualquer coisa antes ou depois desse intervalo
será cortado (não renderizado).

Essas 4 configurações definem um *"frustum"*. O *frustum* é o nome de
uma forma em 3D que é na forma de uma pirâmide com a ponta cortada. Em outras
palavras imagine na palavra "frustum" como outra forma 3D, como esfera,
cubo, prisma, tronco.

<img src="resources/frustum-3d.svg" width="500" class="threejs_center"/>

A altura dos planos `near` e `far` é determinada pelo campo de visão.
A largura de ambos os planos é determinada pelo campo de visão e pelo aspecto.

Qualquer coisa dentro do *frustum*  será renderizado na tela. Qualquer coisa fora
ficará oculta.

O padrão da câmera é olhar para baixo no eixo -Z com +Y para cima. Vamos colocar nosso cubo
na origem, então precisamos mover a câmera um pouco para trás da origem
para ver alguma coisa.

```js
camera.position.z = 2;
```

Abaixo é o que queremos atingir:

<img src="resources/scene-down.svg" width="500" class="threejs_center"/>

No diagrama acima, podemos ver que nossa câmera está em z = 2. Ela está olhando 
para baixo no eixo -Z. Nosso frustum (triângulo azul) inicia 0,1 unidades na frente da câmera 
e vai para 5 unidades à frente da câmera. Como neste diagrama estamos olhando 
para baixo, o campo de visão é afetado pelo aspecto. Nossa tela tem o dobro da 
largura e da altura, de modo que o campo de visão (`fov`) será muito mais amplo do que 
nossos 75 graus especificados, que é o campo de visão vertical.

Em seguida, criaremos uma `Scene`. Uma `Scene` em three.js é a raiz de uma forma gráfica.
Qualquer coisa que você quiser que o three.js desenhe precisa ser adicionado à cena. 
Falaremos com mais detalhes de [como as cenas funcionam em um artigo futuro](threejs-scenegraph.html).

```js
const scene = new THREE.Scene();
```

Em seguida, criaremos uma `BoxGeometry` que contém os dados para uma caixa.
Quase tudo que queremos exibir em Three.js precisa de uma geometria para definir
os vértices que compõem nosso objeto 3D.

```js
const boxWidth = 1;
const boxHeight = 1;
const boxDepth = 1;
const geometry = new THREE.BoxGeometry(boxWidth, boxHeight, boxDepth);
```

Em seguida, criaremos um material básico e definimos sua cor. Cores podem
ser especificadas usando valores de cor hexadecimal de 6 dígitos no estilo CSS padrão. 
A difrença é que no CSS usamos simbolo hash para definir o inicio de um código, e o three.js
requer que usemos a forma matemática de uma cor usando "0x" (0x000000, 0xFFFFFF, etc).

```js
const material = new THREE.MeshBasicMaterial({color: 0x44aa88});
```

Em seguida, criamos uma `Mesh`. Uma `Mesh` em three.js representa a combinação
de uma `Geometry` (a forma do objeto) e um `Material` (como desenhar
o objeto, brilhante ou plano, que cor, que textura(s) aplicar, etc.)
bem como a posição, orientação e escala desse
objeto na cena.

```js
const cube = new THREE.Mesh(geometry, material);
```

E, finalmente, adicionamos essa malha à cena

```js
scene.add(cube);
```

Podemos então renderizar a cena chamando a função de renderização do renderizador
e passando os objetos de cena e da câmera

```js
renderer.render(scene, camera);
```

Aqui está um exemplo prático

{{{example url="../threejs-fundamentals.html" }}}

É meio difícil dizer que é um cubo 3D, pois estamos vendo
diretamente abaixo do eixo -Z e o próprio cubo é alinhado ao eixo
então estamos vendo apenas um único lado do objeto.

Vamos animá-lo, fazendo o objeto girar. Isso deixará mais
claro que está sendo desenhado um objeto em 3D. Para animá-lo, vamos renderizar dentro de um loop de render usando
[`requestAnimationFrame`](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame).

Aqui está o nosso loop

```js
function render(time) {
  time *= 0.001;  // convert time to seconds

  cube.rotation.x = time;
  cube.rotation.y = time;

  renderer.render(scene, camera);

  requestAnimationFrame(render);
}
requestAnimationFrame(render);
```

`requestAnimationFrame` é uma solicitação para o navegador quando você deseja animar algo.
Você passa uma função a ser chamada. No nosso caso, essa função é `render`. O navegador
irá chamar a sua função e se você atualizar algo relacionado à exibição do
página, o navegador irá renderizá-la novamente. No nosso caso, estamos chamando a
função `renderer.render` do three.js que irá desenhar nossa cena.

`requestAnimationFrame` passa o tempo desde o carregamento da página para
nossa função. Esse tempo passa em milissegundos. É muito
mais fácil de trabalhar com segundos, então aqui estamos convertendo para segundos.

Em seguida, definimos a rotação X e Y do cubo para a hora atual. Essas rotações
estão em [radianos](https://en.wikipedia.org/wiki/Radian). Existem 2 pi radianos
em um círculo, então nosso cubo deve girar uma vez em cada eixo em cerca de 6,28
segundos.

Em seguida, renderizamos a cena e solicitamos outro quadro de animação para continuar
nosso loop.

Fora do loop, chamaremos `requestAnimationFrame` uma vez para iniciar o loop.

{{{example url="../threejs-fundamentals-with-animation.html" }}}

Já está um pouco melhor, mas ainda é difícil ver o 3D. O que ajudaria é
adicionar alguma iluminação, então vamos adicionar uma luz. Existem muitos tipos de luzes no
three.js. Falaremos mais sobre luzes em [um artigo futuro](threejs-lights.html). Por enquanto, vamos criar uma luz direcional.

```js
{
  const color = 0xFFFFFF;
  const intensity = 1;
  const light = new THREE.DirectionalLight(color, intensity);
  light.position.set(-1, 2, 4);
  scene.add(light);
}
```

As luzes direcionais têm uma posição e um alvo. Ambos são padronizados para 0, 0, 0. Em nosso
caso estamos definindo a posição da luz para -1, 2, 4, por isso está um pouco à esquerda
acima e atrás de nossa câmera. O alvo ainda é 0, 0, 0, por isso vai brilhar
em direção à origem.

Também precisamos mudar o material do cubo. O `MeshBasicMaterial` não é afetado por
luzes então vamos mudá-lo para um `MeshPhongMaterial` que, este sim, é afetado por luzes.

```js
-const material = new THREE.MeshBasicMaterial({color: 0x44aa88});  // greenish blue
+const material = new THREE.MeshPhongMaterial({color: 0x44aa88});  // greenish blue
```

Aqui está nossa nova estrutura de programa

<div class="threejs_center"><img src="resources/images/threejs-1cube-with-directionallight.svg" style="width: 500px;"></div>

E aqui está funcionando.

{{{example url="../threejs-fundamentals-with-light.html" }}}

Agora isso é claramente im ambiente 3D.

Só por diversão, vamos adicionar mais 2 cubos.

Usaremos a mesma geometria para cada cubo, mas faremos um 
material diferente para que cada cubo possa ter uma cor diferente.

Primeiro faremos uma função que para criar um material novo
com a cor especificada. Em seguida, criaremos uma objeto usando
a geometria especificada e o adicionaremos à cena e
definiremos sua posição X.

```js
function makeInstance(geometry, color, x) {
  const material = new THREE.MeshPhongMaterial({color});

  const cube = new THREE.Mesh(geometry, material);
  scene.add(cube);

  cube.position.x = x;

  return cube;
}
```

Então vamos chamá-lo 3 vezes com 3 cores e posições X diferentes
salvando as instâncias de `Mesh` em um array.

```js
const cubes = [
  makeInstance(geometry, 0x44aa88,  0),
  makeInstance(geometry, 0x8844aa, -2),
  makeInstance(geometry, 0xaa8844,  2),
];
```

Finalmente, giraremos todos os 3 cubos em nossa função de renderização.
Calcularemos uma rotação ligeiramente diferente para cada um.

```js
function render(time) {
  time *= 0.001;  // convert time to seconds

  cubes.forEach((cube, ndx) => {
    const speed = 1 + ndx * .1;
    const rot = time * speed;
    cube.rotation.x = rot;
    cube.rotation.y = rot;
  });

  ...
```

e aqui está.

{{{example url="../threejs-fundamentals-3-cubes.html" }}}

Se você comparar com o diagrama de cima para baixo acima, você pode ver que ele
corresponde às nossas expectativas. Os cubos em X = -2 e X = +2
eles estão parcialmente fora de nosso frustum. Eles também são
um tanto exageradamente deformado desde que o campo de visão
em toda a tela é muito alto.

Nosso programa agora tem esta estrutura

<div class="threejs_center"><img src="resources/images/threejs-3cubes-scene.svg" style="width: 610px;"></div>

Como você pode ver, temos 3 objetos `Mesh`, cada um referenciando o mesmo` BoxGeometry`.
Cada `Mesh` faz referência a um` MeshPhongMaterial` exclusivo para que cada cubo possa ter
uma cor diferente.

Espero que esta breve introdução te ajude a começar. [A seguir vamos falar sobre como
tornar nosso código responsivo para que seja adaptável a vários tamanhos de browser](threejs-responsive.html).

<div class="threejs_bottombar">
<h3>Módulos es6, three.js, e estrutura de pastas</h3>
<p>A partir da versão r106, a forma preferida de usar three.js é por meio de <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import"> módulos es6</a>.</p>
<p>
Módulos es6 podem ser carregados através da palavra-chave <code>import</code>  diretamente no script
ou inline via <code>&lt;script type="module"&gt;</code> tag. Aqui está um exemplo de
ambos
</p>
<pre class=prettyprint>
&lt;script type="module"&gt;
import * as THREE from './resources/threejs/r113/build/three.module.js';

...

&lt;/script&gt;
</pre>
<p>
Os caminhos devem ser absolutos ou relativos. Caminhos relativos sempre começam com <code>./</code> ou <code>../</code>
que é diferente de outras tags como <code>&lt;img&gt;</code> e <code>&lt;a&gt;</code>.
</p>
<p>
As referências ao mesmo script só serão carregadas uma vez, desde que seus caminhos absolutos
sejam exatamente os mesmos. Para o three.js, isso significa que é necessário que você coloque todos os exemplos da
bibliotecas na estrutura de pastas corretamente.
</p>
<pre class="dos">
someFolder
 |
 ├-build
 | |
 | +-three.module.js
 |
 +-examples
   |
   +-jsm
     |
     +-controls
     | |
     | +-OrbitControls.js
     | +-TrackballControls.js
     | +-...
     |
     +-loaders
     | |
     | +-GLTFLoader.js
     | +-...
     |
     ...
</pre>
<p>
A razão pela qual esta estrutura de pasta é necessária é porque os scripts no
exemplos como <a href="https://github.com/mrdoob/three.js/blob/master/examples/jsm/controls/OrbitControls.js"><code>OrbitControls.js</code></a>
têm caminhos relativos codificados como
</p>
<pre class="prettyprint">
import * as THREE from '../../../build/three.module.js';
</pre>
<p>
Usar a mesma estrutura garante então que quando você importar three.js e alguma outra biblioteca do exemplo,
ambas farão referência ao mesmo arquivo three.module.js.
</p>
<pre class="prettyprint">
import * as THREE from './someFolder/build/three.module.js';
import {OrbitControls} from './someFolder/examples/jsm/controls/OrbitControls.js';
</pre>
<p>Isso inclui o uso de um CDN. Certifique-se de que seu caminho para <code>three.modules.js</code> acabe com
<code>/build/three.modules.js</code>. Por examplo</p>
<pre class="prettyprint">
import * as THREE from 'https://unpkg.com/three@0.108.0/<b>build/three.module.js</b>';
import {OrbitControls} from 'https://unpkg.com/three@0.108.0/examples/jsm/controls/OrbitControls.js';
</pre>
<p>Se você preferir o antigo estilo <code>&lt;script src="path/to/three.js"&gt;&lt;/script&gt;</code> style
continue lendo em uma <a href="https://r105.threejsfundamentals.org">versão antiga deste website</a>.
Three.js tem uma política de não se preocupar com a compatibilidade com versões anteriores. Eles esperam que você use uma versão específica, 
já que espera-se que você baixe o código e coloque-o em seu projeto. Ao atualizar para uma versão mais recente
você pode ler o <a href="https://github.com/mrdoob/three.js/wiki/Migration-Guide">guia de migração</a> para ver o que precisa ser alterado. Seria muito trabalhoso manter um módulo es6 e um script de classe
versão deste site, portanto, daqui para frente este site mostrará apenas o estilo do módulo es6. Como afirmado em outro lugar,
para suportar navegadores legados, olhe para um <a href="https://babeljs.io">transpiler</a>.</p>
</div>