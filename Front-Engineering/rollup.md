### rollup.config.js

```javascript
import json from 'rollup-plugin-json'
import commonjs from 'rollup-plugin-commonjs'
import nodeResolve from 'rollup-plugin-node-resolve'
import babel from 'rollup-plugin-babel'

export default {
  input: 'src/main.js',
  output: {
    // file: 'dist/bundle.js',
    dir: 'dist',
    format: 'cjs'
  },
  external: ['jquery'],
  plugins: [
    commonjs(),
    nodeResolve(),
    json(),
    babel({
      // only transpile our source code
      exclude: 'node_modules/**',
      // 默认为false，rollup会自动将babel的helpers抽出来
      // 如果自己配置了@babel/plugin-transform-runtime，就把这一项设置为true
      runtimeHelpers: true
    })
  ]
}

```