---
title: "우아한테크캠프 2020, 2주차 기록"
summary: '이번주에 배운 것을 기록해보자'
layout: wiki
parent: ""
tags: ["Record"]
toc: true
latex: false
date: 2020-07-12T23:40+09:00
lastmod: 2020-06-01T23:25+09:00
---

# 개요
이 글은 babel, webpack, jest를 어떻게 설정할 지 자세하지 않게 말해준다. 조만간 내용은 여러 글로 찢어지게 될 것이다.

더 쓸 것들
- docker-compose
- implementing drag w/o api
	- using  event `mousedown`, `mouseup`
	 
# Setting up babel/webpack
```bash
npm install @babel/core @babel/cli @babel/preset-env --save-dev
```

바벨은 아무일도 하지 않는다. Transpile을 하는 것은 babel plugins. 어떤게 있나:
- @babel/plugin-transform-arrow-functions

여러가지 플러그인을 일일히 설치하는 것을 피곤하니까 그걸 한데 묶어 누가 만든 것이 preset:
- @babel/preset-env
- @babel/preset-react
- @babel/preset-typescript
등등...

@babel/preset-env를 설치하고 설정하자:
```bash
npm install --save-dev @babel/preset-env
```

.babelrc 파일에서
```json
{
  presets: ["@babel/preset-env"]
}
```

그런데 @babel/preset-env가 하는 일이 너무 많아요. 전세계 1%의 브라우저는 버리자:
```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": "last 2 versions, > 1%"
      }
    ]
  ]
}
```
각 브라우저의 마지막 두버전과 점유율이 1%를 넘는 브라우저를 지원한다.
[browserl.ist](https://browserl.ist/?q=last+2+versions%2C+%3E+1%25)를 통해 확인할 수 있다.

## Webpack
이제 webpack을 설정해보자
```bash
npm install --save-dev webpack webpack-cli
```

Webpack이 번들링을 할 때 babel을 사용하기 위해 babel-loader도 사용한다.
```bash
npm install --save-dev babel-loader
```

`webpack.config.js` 파일에서 웹펙 설정을 한다.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist/static"),
    filename: "bundle.js",
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        include: [path.resolve(__dirname, "src")],
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: [
              [
                "@babel/preset-env",
                {
                  target: "last 2 versions, > 1%",
                },
              ],
            ],
          },
        },
      },
    ]
  },
  devtool: "source-map",
  mode: "development",
};
```

웹팩은 entry point에서 시작해 의존성이 있는 것들을 한데 모아 합쳐준다. 결과물을 저장하는 경로와 파일명을 정해줄 수 있다.

여러 파일들을 읽으면서 확장자에 따라 처리하는 일을 다르게 설정할 수 있다. 위의 설정에서는 `./src/` 안에 있는 자바스크립트파일들을 바벨을 이용해 트랜스파일해준다.

다음으로 css를 임포트하면 웹팩은 어떻게 할까?
```bash
npm i -D css-loader sass-loader mini-css-extract-plugin
```

`webpack.config.js` 에서
```javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  module: {
	rules: [
	  ...
	  {
	    test: /\.css$/,
		use: [MiniCssExtractPlugin.loader, "css-loader"],
	  },
	  {
        test: /\.s[ac]ss$/,
        use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "style.css",
    }),
  ],
}
```

이제 웹팩은 파일을 읽어나가다가 css나 sass를 만나면 정리해서 `./dist/static/style.css`로 저장을 해 준다.

그런데 살다보니까 매번 브라우저를 열고 새로고침을 하는 것이 힘들 때가 있다. 이런 사람을 위해 누군가가 `webpack-dev-server`라는 것을 만들어 놨다.

```bash
npm i -D webpack-dev-server
```

```javascript
module.exports = {
  ...
  devServer: {
    contentBase: path.join(__dirname, "./dist"),
    publicPath: "/static/",
    compress: true,
    port: 5500,
    hot: true,
  },
}
```

`contentBase`에 있는 파일들을 static file로 제공해주고 번들링을 한 결과는 따로 파일로 저장하지 않지만 `/static/`에서 제공한다. 예를들면 `localhost:5500/static/bundle.js`가 번들된 자바스크립트파일이다.

그런데 html 파일은 어떻게 할까? 이 역시 플러그인을 사용하자
```bash
npm i -D html-webpack-plugin html-webpack-harddisk-plugin
```

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");
const HtmlWebpackHarddiskPlugin = require("html-webpack-harddisk-plugin")

module.exports = {
  ...
  plugin: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, "./public/index.html"),
      filename: path.resolve(__dirname, "./dist/index.html"),
      inject: true, // inject built script in the end of body tag
      alwaysWriteToDisk: true,
    }),
    new HtmlWebpackHarddiskPlugin(),
  ]
}
```
이제 웹팩은 `./public/index.html` 파일을 여러가지 작업(minify, 헤더에 css 파일 추가, 바디 태그 끝에 번들된 자바스크립트 추가)을 한 후 `./dist/index.html`파일로 저장한다.

그리고 쓰다보니 빌드를 하기 전에 배포파일을 저장하는 폴더를 다 지워주었으면 좋겠다는 생각이 들 수도 있다.
```bash
npm i -D clean-webpack-plugin
```

```javascript
const { CleanWebpackPlugin } = require("clean-webpack-plugin");

module.exports = {
  ...
  plugins: [
    new CleanWebpackPlugin({
      cleanAfterEveryBuildPatterns: ["dist"],
    }),
	...
  ]
}
```

더불어 `package.json`에 스크립트를 만들자:
```json
{
  "scripts": {
    "build": "webpack --config webpack.config.production.js",
    "dev": "webpack-dev-server --open --config webpack.config.dev.js"
  },
}
```
위에는 안썼는데 사실 개발용 설정과 배포용 설정 파일을 나눠놨다. 이것은 숙제 ㅎㅎ

### 참고문헌
- [누군가의 블로그](https://poiemaweb.com/es6-babel-webpack-2)

# Jest를 사용해보자
Jest는 자동으로 테스트를 해준다.

```bash
npm i -D jest
```

`package.json`에서
```json
{
  "scripts": {
    "test": "jest --watch",
  }
}
```

이제 `npm run test`를 하면 테스트를 실행시켜준다. `--watch`는 파일 변경이 있으면 자동으로 테스트를 다시 하라는 의미. 테스트는 `__tests__` 폴더 안에 있는 `.js` 파일이다. 이게 싫으면 그냥 확장자 앞에 `.test` 또는 `.spec`을 붙일 수도 있다. 예를들면 `Component.test.js`. 사실 더 자세하고 설정도 할 수 있는데 이건 숙제.

하나 만들어 보자:

tt.js
```javascript
import "./style.css"

export default function tt(){return 1;}
```

테스트 파일은 
tt.test.js
```javascript
import tt from "./tt"
describe("tt", () =>{
  it("returns 1", () => {
	expect(tt()).toBe(1);
  })
})
```
이제 실행시켜보자.

```bash
npm run test
```

어라? 오류가 난다. `import "./style.css"`를 어떻게 이해할지 모르기 때문. 테스트할때는 스타일이 필요 없으니 대충 속이자.

`jest.config.js`
```javascript
module.exports = {
  moduleNameMapper: {
    "\\.(css|less|sass|scss)$": "<rootDir>/src/lib/__mocks__/styleMock.js",
  },
};
```

그리고 위의 해당 파일(styleMock.js)는 빈 모듈이다:

```javascript
module.exports = {}
```

이러면 jest는 css 파일을 빈 모듈로 착각해 신경쓰지 않고 아무 문제 없이 테스트를 진행한다.

### Dom 테스트가 힘들 때
위의 세팅을 다 해놓고 dom을 테스트하려하는데 막막한가? jest는 기본적인 검사함수밖에 주지 않는다. 이를 불편하게 여긴 어떤 사람이  또 패키지를 만들었다.
```bash
npm i -D @testing-library/dom
```
이 패키지는 getByText(dom, "aaa")과 같은 편리한 함수들을 제공해준다. 사용법은 숙제.
