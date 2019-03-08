## ES6
init:

    npm init -y
查看 Node 已經實現的 ES6 特性

    node --v8-options | findstr harmony
安装 Babel

    npm install --save-dev @babel/core
命令行工具@babel/cli

    npm install --save-dev @babel/cli
  
轉碼規則

    # 最新转码规则
    $ npm install --save-dev @babel/preset-env
    
    # react 转码规则
    $ npm install --save-dev @babel/preset-react
  
建立 .babelrc

    ren aa.txt .babelrc
    
加入至.babelrc

     {
        "presets": [
          "@babel/env",
          "@babel/preset-react"
        ],
        "plugins": []
      }
example.js:

    input.map(item => item + 1);

轉

    npx babel example.js --out-file compiled.js
    
轉碼後compiled.js:

    "use strict";
    
    input.map(function (item) {
      return item + 1;
    });
    
**Traceur**

    npm install -g traceur
    traceur example.js
