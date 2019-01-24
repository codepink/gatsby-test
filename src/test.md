# 번들러

최신 자바스크립트 개발에서 **모듈**은 절대 빠져서는 안 될 용어 중 하나이다. 자바스크립트 파일을 기능 단위로 모듈화하고 이것을 한데 묶어 관리할 방법이 필요하게 되면서 **번들러**의 역할도 중요해졌다. 번들러를 사용하면 소스 코드를 모듈별로 작성할 수 있는 것은 물론 모듈간 또는 외부 라이브러리와의 의존성도 쉽게 관리할 수 있다. 이 가이드는 가장 많이 사용되고 있는 번들러인 webpack을 기반으로 자바스크립트 개발 방식을 설명한다.

들어가기에 앞서 모듈과 번들러에 대해 간략히 소개한다.

### 모듈

전통적인 자바스크립트 개발 방식은 기능별 또는 페이지별로 자바스크립트 파일을 분리하고 HTML에서 `<script>` 태그로 로드하는 것이었다. 하지만 이 방식은 의존성이 있는 코드 사이에 순서를 보장하기 어렵고, 여러 개의 파일을 로드할 때 일부 파일의 문제로 전체 스크립트를 실행하지 못할 수 있는 문제가 있다. 이러한 문제를 해결하기 위해 모듈 단위의 개발 방식이 등장하게 되었다. 그리고 모듈 단위 스코프를 지원하지 않는 브라우저 환경에서 모듈 단위 개발을 위한 노력이 계속되면서 다양한 모듈 포맷이 등장하게 되었다. 모듈은 번들러가 등장하게 된 결정적 요소로써, 다룰 내용이 많으므로 [&#91;FE 개발 가이드&#93; 의존성 관리](https://nhnent.dooray.com/wiki/2349198875637800673/2359190110350812328) 글을 참조하길 바란다.

### 번들러

번들러는 의존성이 있는 모듈 코드를 하나(또는 여러 개)의 파일로 만들어주는 도구이다. 브라우저 환경에서는 CommonJS나 일부 ES6 Mudule로 작성된 코드(크롬과 같은 최신 브라우저에서는 ES6 Mudule을 지원한다)는 바로 실행할 수가 없으므로 모듈 코드를 분석하고 자바스크립트 모듈 스펙에 따라 새로운 코드로 가공이 필요하다(예제 : [https://rollupjs.org/repl](https://rollupjs.org/repl)). 브라우저 환경에서 잘 실행될 수 있도록 가공해주는 것이 번들러의 역할이다. 대표적인 번들러로 [RequireJS](https://requirejs.org/), [Browserify](http://browserify.org/), [Rollup](https://rollupjs.org/guide/en), [Parcel](https://parceljs.org/) 등이 있으며, 현재는 [webpack](https://webpack.js.org/)이 각광받고 있다.

## 목차

* webpack 소개
* webpack 설치 및 실행
* webpack 기본 사용법
    * 번들링
    * 외부 라이브러리 사용 방법
* 설정 파일 다루기 - 기본
    * 환경 설정 파일 생성 및 사용 방법
    * 주요 옵션 설명
* 설정 파일 다루기 - 응용
    * 서비스 코드와 라이브러리 코드 분리하기
    * 서비스와 라이브러리 번들 파일 이름 바꾸기
    * 페이지 단위로 번들 파일 만들기
* webpack-dev-server로 개발하기
* 에셋 관리
    * CSS 파일 관리하기
    * 이미지 파일 관리하기
    * 폰트 로딩
* 트랜스파일을 통한 최신 자바스크립트 개발
* 정적 분석 및 코딩 컨벤션 적용
* 소스맵을 사용한 디버깅
* 맺음말

## webpack 소개

[webpack](https://webpack.js.org/)은 현재 가장 널리 쓰이는 번들러이며, 이 가이드는 webpack 4 기준으로 작성되었다. CommonJS, AMD, ES6 Module(v2부터) 포맷을 모두 지원하며, 자바스크립트뿐만 아니라 CSS, Image 파일 등의 리소스에 대한 의존성도 관리한다. 예를 들어, CSS/Sass/Less 내부에서 사용하는 `@import` , `url(...)` 구문이나 HTML 내부의 `<img src=...>` 태그를 모두 관리한다. 또한 Minify/Uglify, Banner, CSS Preprocess 등 트랜스파일 외의 작업을 자동화해 주는 Task Runner의 기능을 포함하고 있다. 이 외에도 [Code Spliting](https://webpack.js.org/guides/code-splitting/)과 [Dynamic imports\(Lazy Loading\)](https://webpack.js.org/guides/code-splitting/#dynamic-imports), [Tree Shaking](https://webpack.js.org/guides/tree-shaking/), [Dev Server\(Node.js Express 웹서버\)](https://webpack.js.org/configuration/dev-server/) 등 효율적인 자바스크립트 개발을 위한 기능을 제공하고 있다.
![01-webpack-process\(original\).png](/wikis/2349198875637800673/files/2361517705169886096)
[출처](https://medium.com/@paul.allies/webpack-managing-javascript-and-css-dependencies-3b4913f49c58)

## webpack 설치 및 실행

설치 및 실행 방법은 간단하다. 다음 순서로 커맨드를 실행하여 설치한다. 단, 프로젝트별로 다른 버전의 webpack을 사용할 수 있으므로 전역으로 설치하는 것보다 프로젝트 폴더에서 로컬로 설치하는 것을 권장한다. 로컬로 설치하는 경우 `.bin` 폴더에 있는 파일이나 `npx` 명령을 통해 실행할 수 있다. npm을 사용하여 다음 순서로 webpack을 설치한다.

**1. `package.json` 파일 생성**

```
npm init
```

**2\. webpack 설치**

```
npm install --save-dev webpack webpack-cli
```

**3. `src/index.js` 생성**
**4\. webpack 실행**

```
node_modules/.bin/webpack --mode development
```

또는

```
npx webpack --mode development
```

정상적으로 설치 및 실행되었다면, 다음과 같은 메세지가 터미널에 출력된다.
![02-webpack-install.png](/wikis/2349198875637800673/files/2361517827568268586)

다음은 예제를 따라 하면서 모듈을 번들링하는 과정을 살펴보자.

## webpack 기본 사용법

### 번들링

#### 1\. src/index\.js 생성

지금부터는 모듈 파일을 하나씩 추가해볼 것이다. 먼저, 모듈의 시작점이 되는 파일을 추가해야 한다. 이것을 보통 **엔트리 파일**이라고 부른다. 위에서 실습한 내용 중 `src/index.js` 파일이 엔트리 파일에 해당한다. 이 엔트리 파일에서 `sayHello` 모듈을 가져오도록 작성해보자.

``` javascript
import sayHello from './js/sayHello';

console.log(sayHello());
```

#### 2\. src/js/sayHello\.js 생성

`sayHello` 모듈에서는 `sayHello` 함수를 내보낸다.

``` javascript
export default function sayHello() {
  return 'HELLO WEBPACK4';
}
```

#### 3\. npm 스크립트 추가\(package\.json\)

webpack을 실행할 수 있도록 다음 스크립트를 추가한다. `bundle`, `production` 스크립트는 모듈 파일을 묶어 하나의 번들 파일로 만들어주는 작업을 하는데, 번들링 되는 시점에 `mode` 옵션값에 따라 다르게 동작한다.

``` javascript
{
  "scripts": {
    "bundle": "webpack --mode=development",
    "production": "webpack --mode=production"
  }
}
```

##### mode 옵션 사용

webpack 4에서 새로 도입된 빌트인 설정 옵션으로, 옵션값은 `development`, `production`(default), `none`(warning, fallback은 production)이 있다.

###### development 모드

* 개발용 모드이다.
* 소스맵을 제공한다.
* <span style="color:#555555">이전 버전보다 개선된 런타임 에러 메시지를 제공한다.</span>

###### production 모드

* 배포용 모드이다.
* 코드를 압축(Minify), 난독화(Uglify) 한다.
* 그 외에도 최적화(Tree Shaking 등)를 통하여 번들 파일 크기를 작게 만든다.

#### 4\. 번들 파일 생성 및 사용

위에서 추가한 스크립트를 다음 순서로 실행해보고 어떤 차이가 있는지 비교해 보자.

1. `npm run bundle` (development)
2. `dist/main.js` 파일 확인
3. `npm run production` (production)
4. `dist/main.js` 파일 확인

##### npm run bundle 실행 결과

![03-run-bundle.png](/wikis/2349198875637800673/files/2361517920671751237)

##### npm run production 실행 결과

![04-run-production.png](/wikis/2349198875637800673/files/2361517981305079806)

production 모드에서 생성된 번들 파일을 확인하면 난독화된 코드를 볼 수 있다. 또한 development 모드보다 production 모드에서 생성된 번들 파일의 용량이 많이 줄어든 것(5 kb -> 981 bytes)을 확인할 수 있다.

이렇게 생성된 번들 파일은 HTML 파일 내에서 다음과 같이 `<script>` 태그로 로드하여 사용한다. 개별 모듈 파일을 하나씩 추가하는 것이 아니라 webpack 번들링을 통해 모듈화된 파일 1개만 추가하면 된다. HTML 파일을 생성하고 번들 파일을 추가해주는 작업은 webpack 설정만으로도 가능하며 이 과정은 webpack 옵션 설정 단락에서 설명한다.

``` html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>demo</title>
  </head>
  <body>
    <!-- 번들 파일 추가 -->
    <script src="./dist/main.js"></script>
  </body>
</html>
```

### 외부 라이브러리 사용 방법

지금까지 기초적인 번들링 과정을 수행해보았다. 이번에는 직접 구현한 파일 대신 외부 모듈, 즉 라이브러리를 가져오고 번들링할 때 사용해야 하는지 알아보자. 예제에서는 외부 모듈(라이브러리)로 [Lodash](https://lodash.com/)를 사용한다.

#### 1\. Lodash 설치하기

위에서 실습한 프로젝트 폴더에서 다음 커맨드를 실행한다. 잘 설치되었다면 `package.json`의 `dependencies`로 추가되었을 것이다.

```
npm install --save lodash
```

#### 2\. watch 옵션을 사용하여 자동 번들링하기

webpack 빌드를 실행할 때 `watch` 옵션을 줄 수 있다. `watch` 모드에서는 파일이 수정되면 번들 파일이 자동으로 다시 컴파일된다. 파일을 수정할 때마다 번들링 스크립트를 다시 실행해야 하는 수고를 덜어준다. `package.json`에 추가한 스크립트에 옵션을 추가하고, `npm run bundle`을 실행한다. 그다음 `index.js` 파일을 수정했을 때 자동 번들링 되는지 확인해보자.

``` js
"scripts": {
  "bundle": "webpack --watch --mode development",
  ...
},
```

![05-webpack-watch.png](/wikis/2349198875637800673/files/2361518312512412786)

#### 3\. 외부 라이브러리 사용하기

이번에는 Lodash API를 사용해 유효한 텍스트 길이를 확인하는 함수를 만들고 내보내 보자. `src/js/name.js` 파일을 생성한 다음 아래 코드를 추가한다. 이 예제에서는 프로젝트 내에서 생성된 모듈은 상대 경로로 가져오며, npm을 통해 설치된 외부 모듈은 패키지명으로 접근해 사용할 수 있다. 아래와 같이 사용할 경우 모듈 전체를 가져오게 된다. (모듈 일부만 가져오고 싶은 경우에는 [Lodash 사이트](https://lodash.com/)의 "Installation"을 참고한다)

``` javascript
import * as _ from 'lodash';

let minLen = 2;

function isValid() {
  return _.trim(name).length >= minLen;
}

export default {
  isValid
};
```

엔트리 파일(`src/index.js`)에서 Lodash를 사용한 모듈 파일을 가져와서 출력해본다. 다른 외부 모듈도 같은 방식으로 가져와 사용할 수 있다.

``` js
import sayHello from './js/sayHello';
import name from './js/name';

console.log(sayHello());
console.log(name.isValid('Rich'));
```

#### 4\. 번들 파일 생성하기

Lodash를 사용하기 전/후의 번들 파일을 비교해보자. 외부 라이브러리를 사용하는 경우, 번들 파일에 사용한 라이브러리의 코드가 포함된 것을 확인할 수 있다. 굉장히 용량이 큰 라이브러리를 포함하게 되면 번들 파일 사이즈가 커지는 문제가 발생한다. 뒷부분에서 이 문제를 개선할 방법을 다룬다.

## 설정 파일 다루기 - 기본

webpack은 번들링 과정에서 필요한 것을 옵션으로 추가할 수 있는 환경 설정 파일을 제공한다. 설정 파일은 자바스크립트 모듈 형태이며 설정된 옵션을 모듈 객체로 내보낸다. 옵션을 추가하면서 사용법을 확인해 보자.

### 환경 설정 파일 생성 및 사용 방법

프로젝트 폴더의 루트에서 `webpack.config.js` 파일을 생성한다. 다음과 같이 옵션이 추가될 객체를 모듈로 내보낸다. 옵션을 추가한 다음 `npm run bundle`을 실행해 옵션에 따른 각 동작을 확인한다.

``` js
// webpack.config.js
module.exports = {};
```

### 주요 옵션 설명

#### Entry

* 키워드 : `entry`
* 참고 : [https://webpack.js.org/concepts/entry-points/](https://webpack.js.org/concepts/entry-points/)

모듈의 의존성 그래프를 만드는 첫 시작점이다. 이전 목차에서 생성한 `src/index.js` 파일이 기본값으로 설정되며, 지정된 파일에 연관된 모듈과 라이브러리를 포함한 번들을 만든다.

``` javascript
module.exports = {
  entry: './src/index.js'
};
```

다음과 같이 옵션값을 수정하여 엔트리 파일을 변경할 수도 있다.

``` javascript
module.exports = {
  entry: './path/to/my/entry/file.js'
};
```

#### Output

* 키워드 : `output`
* 참고 : [https://webpack.js.org/configuration/output/](https://webpack.js.org/configuration/output/)

번들 파일 위치와 이름을 지정한다. 번들링이 시작되면 `dist` 폴더가 생성되며, 번들링 완료 후에는 `dist` 폴더 아래에 번들 파일이 생성된다. 기본값은 `'./dist/main.js'`이다. 절대 경로를 사용(`path.resolve` 사용)하는 것을 권장한다.

``` javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.js'
  }
};
```

#### Loaders

* 키워드 : `module.rules`
* 참고 : [https://webpack.js.org/configuration/module/#module-rules](https://webpack.js.org/configuration/module/#module-rules)

webpack은 기본적으로 자바스크립트 파일만 인식한다. 로더는 다른 타입의 파일도 처리할 수 있도록 모듈로 변환해주는 도구이다. 다음은 처리할 수 있는 모듈 타입 목록이다.

##### webpack 모듈 예시

* ES2015의 `import`
* CommonJS의 `require()`
* AMD의 `define`, `require`
* CSS/Sass/Less의 `@import`
* 스타일시트의 `url(...)`
* HTML의 `<img src=...>`

##### css-loader를 사용하여 CSS 파일을 모듈로 사용하는 방법

[`css-loader`](https://webpack.js.org/loaders/css-loader/#src/components/Sidebar/Sidebar.jsx)를 사용하면 CSS 파일을 모듈화해준다. `@import`, `url()`을 해석하여 모듈로 만든다. 사용 순서는 다음과 같다.

###### 1\. 로더 설치

``` bash
npm install --save-dev css-loader
```

###### 2\. CSS 파일 생성

모듈로 변환할 CSS 파일(`src/css/style.css`)을 추가한다.

``` css
body {
  background-color: lime;
}
```

###### 3\. CSS 파일 포함하기

엔트리 파일(`src/index.js`)로 돌아가 최상단에 `import` 구문을 사용하여 해당 경로의 CSS 파일을 가져오는 코드를 추가한다. 다른 타입의 파일들도 엔트리 파일로부터 의존성 그래프에 포함되어야 모듈로 변환되기 때문이다.

``` js
...
import './css/styles.css'
...
```

###### 4\. webpack 로더 옵션 수정

webpack 설정 옵션 중 `module.rules`에는 사용하려는 로더의 규칙을 명시하게 된다. 로더명은 `use` 속성에 추가하고, `test` 속성은 해당 로더를 적용하려는 파일 확장자를 정규식 형태로 지정하면 된다. 예제에서는 CSS 파일에만 `css-loader`를 적용하므로, `.scss`, `.less` 등의 스타일 파일들이 있더라도 로더가 적용되지 않는다.

``` javascript
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: 'css-loader'
      }
    ]
  }
};
```

###### 5\. 번들링 실행

번들링한 후 `dist/main.js` 파일에서 `styles.css` 문자열을 검색해보고, CSS 파일로 번들 파일에 포함되었는지 확인해보자.

![06-css-loader.png](/wikis/2349198875637800673/files/2361518489075483710)

#####

> 참고 :
> 다양한 로더 - [https://webpack.js.org/loaders/](https://webpack.js.org/loaders/%5D(https://webpack.js.org/loaders/))

#### Plugins

* 키워드 : `plugins`
* 참고 : [https://webpack.js.org/configuration/plugins/](https://webpack.js.org/configuration/plugins/)

플러그인은 모듈 변환 외에 더 다양한 처리를 할 수 있는 도구를 제공한다. 예를 들어 번들 최적화, 에셋 관리 등을 처리할 수 있다. 플러그인 인스턴스를 생성(`new` 키워드 사용)하여 옵션에 전달하는 방식으로 사용된다.

##### HtmlWebpackPlugin, CleanWebpackPlugin 사용 방법

다음 플러그인을 설치해보자. [`HtmlWebpackPlugin`](https://webpack.js.org/plugins/html-webpack-plugin/)은 HTML 파일을 생성하고, `<script>` 태그로 번들링된 모든 파일을 HTML에 삽입해준다. [`CleanWebpackPlugin`](https://webpack.js.org/guides/output-management/#cleaning-up-the-dist-folder)은 번들이 시작되기 전에 특정 폴더를 지워주는 플러그인으로 주로 빌드 폴더를 정리하는 데 사용된다.

###### 1\. 플러그인 설치

``` bash
npm install --save-dev html-webpack-plugin clean-webpack-plugin
```

###### 2\. webpack\.config\.js 수정

``` javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  ...
  plugins: [
    new HtmlWebpackPlugin(),
    new CleanWebpackPlugin(['dist'])
  ]
};
```

###### 3\. 번들링 실행

번들링을 실행하면 `CleanWebpackPlugin` 플러그인을 통해 `dist` 폴더가 삭제되었다가 다시 생성되며, `dist/index.html` 파일이 추가된 것을 확인할 수 있다. 이때 생성된 `index.html`에서는 번들 파일 `main.js`를 로드한다.

> 참고 :
> 다양한 플러그인 -  [https://webpack.js.org/plugins/](https://webpack.js.org/plugins/)

#### Mode

* 키워드 : `mode`
* 참고 : [https://webpack.js.org/concepts/mode/](https://webpack.js.org/concepts/mode/)

<span style="color:#555555">이전 목차에서 다루었던 </span>`scripts`<span style="color:#555555">의 </span>`mode`<span style="color:#555555"> 옵션을 직접 설정해보자.</span> <span style="color:#555555">이미 짐작했겠지만, 개발 환경과 프로덕션 환경을 나누는 이유는 번들링되는 시점에 하는 일이 달라야 하기 때문이다.</span> <span style="color:#555555">간단한 예로, 프로덕션 환경에서는 파일 용량을 줄이기 위해 압축이 필요하지만, 개발 환경에서는 디버깅을 위해 압축은 필요하지 않다.</span> 이러한 일을 나누어서 처리해주는 것이 `mode` 옵션이며, <span style="color:#555555">이 값에 따라 webpack이 번들링에 필요한 작업을 알아서 처리한다.</span> `scripts` 옵션과 같이 `development`, `production`, `none`으로 값을 설정할 수 있다. 다음은 환경에 따라 다른 설정 파일을 사용하는 예제를 만들어보겠다.

###### 1\. 환경 설정 파일 분리

지금까지는 1개 환경 설정 파일을 사용하였다. webpack이 실행되면 기본적으로 `webpack.config.js` 파일이 실행되며, 만약 다른 설정 파일을 실행하고 싶다면 다음과 같이 `config` 옵션을 사용하면 된다.

``` bash
webpack --config webpack.custom.config.js
```

개발, 프로덕션 환경에서 각각 실행될 설정 파일을 생성한다.

* 개발 환경 실행 : `webpack.dev.config.js`

``` javascript
module.exports = {
  mode: 'development',
  ...
};
```

* 프로덕션 환경 실행 : `webpack.config.js`

``` javascript
module.exports = {
  mode: 'production',
  ...
};
```

###### 2\. 스크립트 수정

``` js
"scripts": {
  "bundle": "webpack --watch --config webpack.dev.config.js",
  "production": "webpack" // webpack.config.js 기본 실행
},
```

###### 3\. 번들링 실행

`webpack --mode` 옵션을 사용했을 때와 같은 결과의 파일들이 생성되는지 확인해본다.

## 설정 파일 다루기 - 응용

이번에는 실제 서비스 개발에서 유용하게 사용되는 옵션을 소개하고자 한다.

### 서비스 코드와 라이브러리 코드 분리하기

최종적으로 **webpack으로 번들링 된 파일은 1개**다. 그러나 분명히 번들링에 사용되는 모듈 중 일부 모듈을 분리해야 하는 상황이 발생하게 된다. 예를 들어 무거운 외부 라이브러리를 여러 개 포함한 번들 파일을 로드하는 웹앱이 있을 때 초기 로딩 속도를 느리게 할 수 있으므로, 이러한 경우에는 서비스 코드와 라이브러리 코드를 분리하여 사용하는 것이 좋다. 성능 문제 또는 관리 목적 때문에 모듈 파일을 분리해 사용해야 한다. webpack 옵션을 통해 모듈 파일을 분리하는 방법을 알아보자.

#### SplitChunksPlugin

* 키워드 : `optimization.splitChunks`
* 참고 : [https://webpack.js.org/plugins/split-chunks-plugin/](https://webpack.js.org/plugins/split-chunks-plugin/)

webpack 4부터 지원하는 옵션으로, webpack 3까지 사용하던 [`CommonChunkPlugin`](https://webpack.js.org/plugins/commons-chunk-plugin/)이 내장된 형태이다. 이 옵션을 사용하면, 번들링할 때 공통으로 사용하는 모듈 또는 라이브러리를 별도의 청크(chunk)로 분리한다. 기본값은 `node_modules` 폴더의 모듈을 대상으로 청크를 분리한다. 아래와 같이 `splitChunks` 옵션 중 `name`을 설정하면 해당 설정값을 이름으로 한 파일이 생성된다.

``` javascript
module.exports = {
  ...
  optimization: {
    splitChunks: {
      chunks: 'all',
      name: 'vendors'
    }
  }
};
```

번들링을 실행하면 `dist` 폴더 안에 `main.js`(서비스 코드) 파일 외 `vendor.js`(라이브러리 코드) 파일이 추가로 생성된다. 외부 라이브러리인 Lodash 코드가 `vendor.js` 파일로 분리된 것을 확인할 수 있다.

### 서비스와 라이브러리 번들 파일 이름 바꾸기

지금까지 `entry` 옵션을 사용해 엔트리 파일을 지정했다. `entry` 옵션값은 기본적으로 문자열 타입(Single Entry Syntax)을 사용하나, **객체 타입(Multi Entry Syntax)** 을 사용할 수도 있다. 다음과 같이 설정하면 위에서 분리된 서비스 코드의 번들 파일명이 `main.js`에서 `app.js`로 변경된다.

``` javascript
module.exports = {
  ...
  entry: {
    app: './src/index.js'
  },
  ...
};
```

또한 `output` 옵션을 사용하여 변경할 수 있다. 번들 파일이 생성될 때 `output.filename`에 지정된 값으로 파일 이름이 변경되며, `[name]`은 생성되는 각 번들 파일 이름에 해당한다. `cutsom-app.js`, `custom-vendor.js` 파일이 생성되는 것을 확인할 수 있다.

``` javascript
module.exports = {
  ...
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'custom-[name].js'
  },
  ...
};
```

### 페이지 단위로 번들 파일 만들기

페이지 단위로 작게 번들 파일을 생성하면, 큰 하나의 번들 파일을 구성하는 것보다 여러 이점이 있다. 페이지 단위의 번들 파일을 생성해보자. 먼저, 다음과 같이 서브 페이지에서만 사용될 `src/sub/index.js` 파일을 추가한다.

``` javascript
import * as _ from 'lodash';

console.log(_.toLength('sub page'));
```

위에서 사용한 `entry`, `splitChunks` 옵션을 사용해 번들링 한다.

``` javascript
module.exports = {
  ...
  entry: {
    app: './src/index.js',
    subPage: './src/sub/index.js'
  },
  optimization: {
    splitChunks: {
      chunks: 'all',
      name: 'vendors'
    }
  },
  ...
};
```

HTML 파일에서 다음과 같이 사용하면 된다.

``` html
// main page
...
<script src="./dist/vendors.js"></script>
<script src="./dist/app.js"></script>
...
```

``` html
// sub page
...
<script src="./dist/vendors.js"></script>
<script src="./dist/subPage.js"></script>
...
```

## webpack-dev-server로 개발하기

이전 예제에서는 개발과 프로덕션 환경을 분리해 사용했다. [`webpack-dev-server`](https://github.com/webpack/webpack-dev-server)는 개발자의 로컬 PC에서 서버를 띄우고, 브라우저에서 직접 번들 파일을 확인할 수 있는 기능을 제공한다. 별도의 서버 구성이나 번들 파일이 포함된 HTML 페이지를 생성하는 복잡한 작업을 하지 않고 현재 개발 중인 페이지를 쉽게 확인할 수 있다.

### 설치 및 실행

다음 커맨드를 실행해 `webpack-dev-server`를 설치한다.

``` bash
npm install --save-dev webpack-dev-server
```

서버를 띄우기 위한 스크립트를 추가하고 실행해본다. `mode` 옵션을 사용하면 해당 모드의 기본 설정값을 기반으로 서버가 실행된다. 아래는 `development` 모드이다.

``` javascript
// package.json
{
  "scripts": {
    ...
    "dev": "webpack-dev-server --open --mode=development"
  }
}
```

![07-webpack-dev-server.png](/wikis/2349198875637800673/files/2361518601798878368)

지금부터는 예제의 설정을 변경해 브라우저에서 번들 파일을 확인하고 개발하는 방법을 알아보자. 개발 환경에서 실행되는 설정 파일(`webpack.dev.config.js`)에 다음과 같은 옵션들이 지정되어 있는지 확인한다. `HtmlWebpackPlugin`을 사용하면 자동으로 `dist/index.html` 파일을 만들어주고 이 파일에서는 번들 파일 `main.js`를 로드한다. 그리고 서버에 접속하면 생성된 `index.html` 파일이 열린다.

``` js
// webpack.dev.config.js

const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  mode: 'development',
  module: {
    rules: [
      {
        test: /\.css$/,
        use: 'css-loader'
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin(),
    new CleanWebpackPlugin(['dist'])
  ]
};
```

![08-webpack-dev-server-live.gif](/wikis/2349198875637800673/files/2361518752108507810)

### 실시간 반영

webpack에서는 변경된 모듈을 실시간으로 번들 파일에 적용하고 업데이트해 주는 기능을 제공한다. [HMR\(Hot Module Replacement\)](https://webpack.js.org/guides/hot-module-replacement/)을 사용하면 파일을 수정할 때마다 페이지 리로드 없이 변경된 부분의 모듈만 업데이트되는 것을 확인할 수 있다. `HotModuleReplacementPlugin`을 추가하고 `devServer` 옵션을 설정해보자.

``` js
...
const webpack = require('webpack');

module.exports = {
  ...
  plugins: [
    ...
    new webpack.HotModuleReplacementPlugin()
  ],
  devServer: {
    hot: true,
    inline: true
  }
};
```

![09-HMR.gif](/wikis/2349198875637800673/files/2361519339658685458)

### 그 외 설정값

* `host` : 기본값이 `localhost`이므로 외부에서 접속하기 위해서 설정한다.
* `port` : 기본값은 `8080`으로 포트 번호를 변경할 때 사용한다.

``` javascript
module.exports = {
  ...
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ],
  devServer: {
    hot: true,
    inline: true,
    host: '0.0.0.0',
    port: 8080
  }
};
```

## 에셋 관리

### CSS 파일 관리하기

#### style-loader 사용

지금까지 예제를 잘 따라왔다면 이전 실습 과정에서 이상한 점을 눈치챘을 것이다. 스타일을 모듈로 가져왔음에도 불구하고 `index.html`에 스타일이 적용되지 않았다. 모듈로 변경된 CSS 스타일은 DOM에 추가되어야 브라우저가 해석할 수 있다. 즉, `<style>` 태그를 DOM에 추가하는 과정이 필요하며 [`style-loader`](https://webpack.js.org/loaders/style-loader/#src/components/Sidebar/Sidebar.jsx)가 이를 처리해준다. 다음과 같이 `style-loader`를 설치해본다. 반드시 `css-loader`보다 `style-loader`가 먼저 로드되어야 하므로 `use` 속성의 배열 값에 다음과 같은 순서로 명시해야 한다.

``` bash
npm install --save-dev style-loader
```

``` javascript
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader'
        ]
      }
    ]
  }
  ...
};
```

#### CSS 파일로 분리하기

CSS 파일을 모듈로 가져올 경우, 번들 파일에 CSS 스타일이 모두 포함된다. 서비스 페이지에서 사용하기 위해서는 아래와 같이 플러그인을 설정한다.

이전 버전까지 많이 사용되던 `extract-text-webpack-plugin`는 아직 webpack 4에 대응 중(프리릴리즈인 4.0.0-beta.0를 사용하려면 `extract-text-webpack-plugin/@next` 버전 사용)이므로 `mini-css-extract-plugin`을 사용한다.

``` bash
npm install --save-dev mini-css-extract-plugin
```

``` javascript
...
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    ...
    new MiniCssExtractPlugin()
  ],
  ...
};
```

번들링을 실행하면, `dist` 폴더 안에 `app.css` 파일이 생성되고 `index.html`에 CSS 파일을 로드하는 태그가 추가된 것을 확인할 수 있다.

### 이미지 파일 관리하기

번들링이 완료된 시점에 `dist` 폴더에는 에셋 파일이 위치해야 한다. 또한 에셋을 사용하는 파일은 변경된 경로를 포함하고 있어야 한다. `file-loader`는 이미지 파일에 필요한 일련의 과정을 처리해준다.

#### 1\. 로더 설치

``` bash
npm install --save-dev file-loader
```

#### 2\. src/css/styles\.css 편집

`images` 폴더를 생성하고 그 아래 스타일에서 사용할 이미지를 추가한다. 그리고 `styles.css`를 아래와 같이 변경한다.

``` css
body {
  background-image: url('../images/image.png');
}
```

#### 3\. 설정 파일 옵션 추가

`webpack.dev.config.js`, `webpack.config.js` 파일에 이미지에 필요한 로더 설정을 추가한다.

``` javascript
module.exports = {
  ...
  module: {
    rules: [
      ...
      {
        test: /\.(gif|png|jpe?g|svg)$/i,
        use: [
          'file-loader'
        ]
      }
    ]
  },
  ...
};
```

#### 4\. 번들링 실행

번들링을 실행(`npm run production`)하면 `dist/[hash].png` 파일이 생성된 것을 확인할 수 있다. 또한 `app.css` 파일에서도 이미지 경로가 변경되었다.

### 폰트 로딩

이미지처럼 폰트도 모듈화하여 가져와야 한다. 이번에도 `file-loader`를 사용한다.

#### 1\. 스타일 추가

`fonts` 폴더를 생성하고 그 아래 [폰트를 추가](https://www.google.com/get/noto/)한다. `src/css/styles.css` 파일에서 다음과 같이 폰트 스타일을 추가한다.

``` css
@font-face {
  font-family: 'noto-sans';
  src: url('../fonts/NotoSans-Black.ttf');
}

body {
  font-size: 12px;
  font-family: 'noto-sans';
  background-image: url('../images/image.png');
}
```

#### 2\. HTML 파일 수정

`webpack-dev-server`를 실행하면 `dist` 폴더 하위에 `index.html` 파일을 생성하고 번들링된 모듈 파일을 HTML 안에 삽입해준다. 폰트 로드 예제를 확인하기 위해서 `index.html` 파일에 폰트 스타일과 관련된 태그(`<h1/>`)를 추가해야 하는데, 다른 HTML 파일을 생성하여 `index.html`을 대체한다. 다음과 같이 `demo/index.html` 파일을 생성하고, `HtmlWebpackPlugin` 플러그인의 `template` 옵션에 `demo/index.html` 파일을 지정하면 된다.

``` html
// demo/index.html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>demo</title>
  </head>
  <body>
    <h1>Hello Wabpack!<h1>
  </body>
</html>
```

``` js
// webpack.dev.config.js
module.exports = {
  ...
  plugins: {
    new HtmlWebpackPlugin({
      template: 'demo/index.html'
    }),
    ...
  },
  ...
};
```

#### 3\. 설정 파일 옵션 추가

`webpack.dev.config.js`, `webpack.config.js` 파일에 폰트에서 대한 로더 설정을 추가한다.

``` javascript
module.exports = {
  ...
  module: {
    rules: [
      ...
      {
        test: /\.(woff|woff2|eot|ttf|otf|)$/,
        use: [
          'file-loader'
        ]
      }
    ]
  },
  ...
};
```

#### 4\. 번들링 실행

먼저, `webpack-dev-server`를 실행해서 `<h1>Hello Wabpack!<h1>` 텍스트에 폰트가 잘 적용되었는지 확인해본다. 그다음 번들링을 실행하면 이미지 파일과 같이 `dist` 폴더 안에 폰트 파일이 생성된 것을 확인할 수 있다.

![11-asset-font.png](/wikis/2349198875637800673/files/2361521538215557549)

## 하위 브라우저 대응

### 트랜스파일을 통한 최신 자바스크립트 개발

최신 자바스크립트 코드는 ES6로 작성되는 경우가 많다. 그러나 하위 브라우저에서는 ES6 코드를 실행할 수 없으므로, ES6로 개발하고 ES5로 트랜스파일해서 사용하는 과정을 거쳐야 한다. 이때 `babel-loader`를 사용한다. 예제에서는 [Babel 7](https://babeljs.io/docs/en/v7-migration)을 기준으로 설명한다.

#### 1\. 로더 및 Babel 설치

``` bash
npm install --save-dev babel-loader @babel/core @babel/preset-env
```

#### 2. Babel 설정 파일 생성

`.babelrc`는 최신 명세로 작성된 자바스크립트 코드를 Babel로 어느 범위까지 변경해줄 것인지 설정하는 파일이다. 더 자세한 사용 방법은 [문서](https://babeljs.io/docs/en/config-files)를 참조한다.

``` json
{
  "presets": ["@babel/preset-env"]
}
```

#### 3\. webpack 설정 파일 옵션 추가

다른 로더들과 동일한 방식으로 옵션 추가한다. 이 때, `node_modules` 폴더 이하의 파일들은 트랜스파일할 필요가 없으므로 폴더 전체를 제외(`exclude`)시킨다. 트랜스파일링 된 번들 파일은 프로덕션 환경에서 필요하므로 `webpack.config.js` 파일을 수정한다.

``` javascript
// webpack.config.js
module.exports = {
  ...
  module: {
    rules: [
      ...
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: [
          'babel-loader'
        ]
      }
    ]
  },
  ...
};
```

#### 4\. ES6 코드 작성

간단한 트랜스파일을 확인하기 위한 용도로 `src/index.js` 파일을 작성한다. `let` 키워드를 지원하는 브라우저는 [다음](https://caniuse.com/#feat=let)과 같으며, 인터넷 익스플로러 11 미만 브라우저에서 번들 파일이 실행되도록 트랜스파일링 되어야 한다.

``` js
let foo;

if (true) {
  foo = 1;
} else {
  foo = 2;
}

console.log(foo);
```

#### 5\. 번들링 실행

`babel-loader`를 사용한 경우와 사용하지 않은 경우를 나누어 각각 번들링하고 파일을 비교해보면,

* 로더를 사용한 경우

``` js
... var r;r=1,console.log(r) ...
```

* 로더를 사용하지 않은 경우

``` js
... let r;r=1,console.log(r) ...
```

로더를 사용하였을 때 `let` 키워드가 `var`로 변환된 것을 볼 수 있다. 이렇게 Babel 로더를 사용하면, 최신 스펙으로 작성된 자바스크립트 코드를 트랜스파일하여 구형 브라우저에 대응할 수 있다.

## 정적 분석 및 코딩 컨벤션 적용

자바스크립트는 컴파일 단계가 없으므로 코드를 실행하기 전까지는 오류를 확인할 수 없다. 에러의 많은 부분이 오타나 문법 오류 등의 실수로 발생한다. 이러한 문제점을 해결하기 위해 **정적 분석 도구**의 도움이 필요하다. 정적 분석 도구에 대한 자세한 설명은 [&#91;FE 개발 가이드&#93; 정적 분석](https://nhnent.dooray.com/project/pages/2354102384017570716)을 참조하길 바란다. 가장 많이 사용되는 도구로는 [ESLint](https://eslint.org/)가 있으며, 예제는 ESLint 5를 기준으로 설명한다.

#### 1\. ESLint 및 로더 설치

``` bash
npm install -save-dev eslint eslint-loader
```

#### 2\. ESLint 설정 파일 생성

* `node_modules/.bin/eslint --init` 혹은 `npx eslint --init` 실행한다.
* 옵션 설정과 관련된 질문이 나오면 "Answer question about your style" 선택한다.
* 옵션 설정이 끝나면 `.eslintrc.js` 파일이 생성된다.

``` js
module.exports = {
    "env": {
        "browser": true,
        "commonjs": true,
        "es6": true
    },
    "extends": "eslint:recommended",
    "parserOptions": {
        "ecmaVersion": 2015,
        "sourceType": "module"
    },
    "rules": { ... }
};
```

#### 3\. webpack 설정 파일 옵션 추가

번들링 시점에 자바스크립트 파일 중에 오류가 있는 코드를 분석해주도록 `eslint-loader` 설정을 추가해준다. `webpack.dev.config.js`, `webpack.config.js` 파일에 모두 설정해주는 것이 좋다.

``` javascript
// webpack.config.js
module.exports = {
  ...
  module: {
    rules: [
      ...
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: [
          'babel-loader',
          'eslint-loader'
        ]
      }
    ]
  },
  ...
};
```

##### eslint-config-tui 적용하기

[eslint-config-tui](https://github.com/nhnent/tui.eslint.config)를 사용하면 **FE개발랩에서 사용하고 있는 ESLint 컨벤션 규칙**을 적용할 수 있다.

``` bash
npm install --save-dev eslint-config-tui
```

해당 모듈을 설치한 후, `.eslintrc.js` 파일 옵션을 다음과 같이 수정한다.

``` js
module.exports = {
  ...
  "extends": "tui/es6",
  "parserOptions": {
    "sourceType": "module"
  }
}
```

#### 4\. 번들링 실행

번들링될 때 ESLint를 통해 코드를 분석하고, 코드에 문법적인 오류가 있으면 다음과 같이 에러 메시지를 표시한다.

![12-eslint-loader.gif](/wikis/2349198875637800673/files/2361521786919257098)

## 소스맵을 사용한 디버깅

소스맵(Source Map)은 트랜스파일, 압축/난독화된 코드를 실제 코드와 대응시켜주는 기술이다. 표준 스펙은 아니지만, 대부분의 최신 브라우저(인터넷 익스플로러 11 이상)에서 지원한다. (현재 v3 스펙 : [구글 문서](https://docs.google.com/a/logentries.com/document/d/1U1RGAehQwRypUTovF1KRlpiOFze0b-_2gc6fAH0KY0k) 참조) JSON 형태의 파일이며, 맵핑 정보를 base64 형태로 저장한다. webpack을 포함한 다양한 도구에서 지원한다.

### 소스맵 활성화

#### 1\. webpack 설정 파일 옵션 추가

압축/난독화된 코드에 소스맵이 추가되도록 프로덕션 환경 설정 파일에 옵션을 추가한다.

``` javascript
// webpack.config.js
module.exports = {
    ...
    devtool: 'source-map'
};
```

#### 2\. 번들링 실행

번들링을 하면 번들 파일 하단에 `//# sourceMappingURL=app.js.map` 주석이 추가된다. 또한 맵핑용 번들 파일(`[bundle-filename].js.map`)이 생성된다. 이 한 쌍의 파일로 압축된 코드를 디버깅할 수 있다.

![13-sourcemap-bundling.png](/wikis/2349198875637800673/files/2361521871869839592)

#### 3\. 크롬 개발자 도구로 디버깅하기

크롬 개발자 도구의 Sources 탭을 확인하면 소스맵으로 실제 코드를 추적할 수 있다. 소스맵을 사용하여 디버깅하기 위해 크롬 개발자 도구 설정에서 "소스맵 활성화"를 해야 한다.

![14-chrome-sourcemap.png](/wikis/2349198875637800673/files/2361521937406680726)

아래는 소스맵을 사용하였을 때와 사용하지 않았을 때 차이를 보여준다. 소스맵을 사용하는 경우, 개발자 도구에서 압축된 파일과 함께 실제 코드도 모두 가져온다. 압축된 코드에서 에러가 발생하더라도 소스맵으로 실제 코드를 추적하여 쉽게 원인을 파악할 수 있다. 소스맵으로 디버깅을 해보자.

##### 소스맵을 사용하지 않은 경우

![15-sourcemap-none.gif](/wikis/2349198875637800673/files/2361522026826631186)

##### 소스맵을 사용한 경우

![16-using-sourcemap.gif](/wikis/2349198875637800673/files/2361522284213095042)

## 맺음말

지금까지 webpack 사용 방법을 익히면서 번들러의 역할에 대해 알아보았다. 여러 예제를 통해 보았듯이, webpack은 개발자가 신경 써야 할 많은 부분(개발 환경이나 용량 등)을 다양한 옵션과 플러그인으로 제공하여 개발의 편의성을 높여준다. 이 가이드가 실무에 모듈 단위 개발과 번들러를 적용하는 데 도움이 되길 바란다.

---

이 문서의 내용과 연관된 FE개발랩 사내 교육은 아래와 같다. 추가로 교육을 수강할 것을 권장 한다.

- 웹팩을 이용한 자바스크립트 실전 개발
- 프론트엔드 성능

- - -

이 문서는 NHN Ent.의 FE개발랩에서 작성하고 관리하는 공식 웹 프론트 개발 가이드이다.
가이드 적용 관련 문의나 문서의 오류, 개선 제안은 공식 문의 채널(dl_javascript@nhnent.com)을 통해 할 수 있다.

- - -

<br>
<br>
