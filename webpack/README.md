# Webpack Tip

## 기본 Config 정의

```js
var path = require('path');
var webpack = require('webpack');

module.exports = {
    entry: './reactStartup.js',
    output: {
        filename: 'bundle.js',
        path: path.resolve(__dirname, 'dist')
    },
    resolve: {
        alias: {
            'simple-react-bootstrap': 'node_modules/simple-react-bootstrap/dist/simple-react-bootstrap.js',
            'jscolor': 'util/jscolor.js'
        },
        modules: [
            path.resolve('./'),
            path.resolve('./node_modules'),
        ]
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: 'babel-loader',
                options: {
                    presets: ['react', 'es2015']
                },
                include: [
                    resolve('src'),
                    resolve('test'),
                    /stove-common/               // es6 로 되어있는 node_modules 내 모듈이 있다면 지정을 해주어야 한다.
                ]
            }
        ]
    }
};
```

## 트리쉐이킹
### requirements
1. webpack 내장 uglify 사용할경우
`babel-core` `babel-loader` `babel-preset-env`

2. 별도 uglify 사용할 경우
`uglifyjs-webpack-plugin`과 추가로 uglify-js 버전 지정 필요시 `uglify-js`

사용 `webpack.config.js`
```
plugins: [
    new webpack.optimize.UglifyJsPlugin()
    // or
    new webpack.optimize.UglifyJsPlugin({
        compress: {     // compress 지정해줄경우 warnings가 표시된다 default: true
            warnings: false // 메시지 안보고싶을경우
            unused: true    // default: true        // 트리쉐이킹에 필요
        },
        beautify: true      // 가독성여부(ture일 경우 minify 하지않음) default: false       // 트리쉐이킹 확인하고 싶을경우 true
        mangle: false       // 난독화 default: true                                         // 트리쉐이킹 확인하고 싶을경우 false
        output: {
            comments: true  // 주석 살림 default: false
        }
    })
    // or 
    UglifyJSPlugin = require('uglifyjs-webpack-plugin')
    
    new UglifyJSPlugin()
]
```

`.babelrc`
```
{
    "presets": [
        [
            "env": {
                "modules": false            // false도 uglifyPlugin설정과 더불어 같이 지정해야만 트리쉐이킹이 된다.
            }
        ]
    ]
}
```

## 모듈리소스 CDN 처리
### webpack-cdn-plugin 사용
```
new WebpackCdnPlugin({
    modules: [
        {
            name: 'froala-editor',
            var: 'froala-editor',
            style: 'css/froala_editor.pkgd.min.css',
            cssOnly: true
        },
        {
            name: 'froala-editor',
            var: 'froala-editor',
            style: 'css/froala_style.min.css',
            cssOnly: true
        },
        {
            name: 'font-awesome',
            var: 'font-awesome',
            style: 'css/font-awesome.min.css',
            cssOnly: true
        },
        {
            name: 'jquery-ui-dist',
            var: 'jqueryui',
            style: 'jquery-ui.min.css',
            cssOnly: true
        },
        {
            name: 'swiper',
            var: 'Swiper',
            style: 'css/swiper.min.css',
            cssOnly: true
        },
        {
            name: 'jquery.scrollbar',
            var: 'jquery.scrollbar',
            style: 'jquery.scrollbar.min.css',
            cssOnly: true
        }
    ],
    prodUrl: '//cdnjs.cloudflare.com/ajax/libs/:var/:version/:path'
})
```

## lodash 사용하는 함수만 로드
필요 devDependencies: babel-plugin-transform-runtime
> .babelrc 설정
```js
"plugins": [
    "transform-runtime",
    ["transform-imports", {
      "lodash": {
        "transform": "lodash/${member}",
        "preventFullImport": true
      }
    }]
  ]
```

```vue
import {find} from 'lodash';
```