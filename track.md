## track

`mkdir debug-react`

add `package.json` `.gitignore` `pnpm-workspace.yaml`

`pnpm create vite`

```
√ Project name: ... debug
√ Select a framework: » React
√ Select a variant: » TypeScript
```

`git clone https://github.com/facebook/react.git`

`react` 目录下

`git checkout v18.2.0`

`rm -rf .circleci .codesandbox .git .github fixtures` delete some directories

`yarn`

modify `react\scripts\rollup\build.js`
`getRollupOutputOptions` 设置 `sourcemap: true`
`getPlugins` 屏蔽 `transform` `isProduction` `shouldStayReadable` `renderChunk` 相关插件

modify `react\scripts\rollup\packaging.js`
`node_modules` -> `weiyi`
`node_modules` 这个目录在 pnpm workspace 会有影响。

`yarn build react/index,react/jsx,react-dom/index,scheduler --type=NODE`

`debug-react` 目录下

`pnpm i react react-dom -F debug --workspace`

modify `debug\vite.config.ts`

```
export default defineConfig({
  plugins: [react()],
  optimizeDeps:{
    include:["react-dom/client"]
  }
})
```

`pnpm -F debug dev`

launch debug
注意：文件目录结构不同，`webRoot` 不一样。

## `import ReactDOM from 'react-dom/client'` error in workspace mode

```
Uncaught SyntaxError: The requested module '/@fs/D:/cycle-pool/debug-react-w/react/build/wc/react-dom/client.js' does not provide an export named 'default' (at main.tsx:2:8)
```

https://github.com/vitejs/vite/issues/3024#issuecomment-896072289
https://github.com/vitejs/vite/issues/3024#issuecomment-907759046
https://github.com/vitejs/vite/issues/3024

`debug\vite.config.ts`

```
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  optimizeDeps:{
    include:["react-dom/client"]
  }
})
```

## files

package.json

```
{
  "name": "debug-react",
  "private": true,
  "scripts": {
    "preinstall": "npx only-allow pnpm"
  }
}
```

pnpm-workspace.yaml

```
packages:
  - 'debug'
  - 'react/build/weiyi/*'
```

.gitignore

```
node_modules/
```

`.vscode\launch.json`

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:5173",
            "webRoot": "${workspaceFolder}/debug"
        }
    ]
}
```
