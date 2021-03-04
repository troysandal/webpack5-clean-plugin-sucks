# TypeScript, WebPack, Clean, Pain

This project demonstrates TypeScript compilation errors produced when adding TypeScript to a bare bones Webpack project that uses the [clean-webpack-plugin](https://www.npmjs.com/package/clean-webpack-plugin), a plugin recommended by Webpack 5.

I discovered this issue in another project that was already successfully using webpack.  Wen I decided to add TypeScript support to it and ran my first compile via `npx tsc` I got a slew of errors. This project is a bare bones example of the problem


If you run `npx tsc` you'll see 89 compiler errors, here's the first few...

``` sh
clean-webpack-plugin/
@types/webpack/index.d.ts(32,3): error TS2305: Module '"../../tapable/tapable"' has no exported member 'Tapable'.

@types/webpack/index.d.ts(32,3): error TS2305: Module '"../../tapable/tapable"' has no exported member 'Tapable'.
```

## Culprit?
The problem *appears* to be the way in which `clean-webpack-plugin` depends upon `"@types/webpack": "4.4.31"` which in turn depends on `@types/tapable: "*"` expection v1.x of tapables. `webpack/package.json` however, depends upon `tapable: 2.1.1` hence the errors above.

## Fixes
There are three fixes I found, not sure which is best yet:

1. `rm -rf node_modules/@types/webpack`
1. Set `skipLibCheck:true` in the tsconfig.json compilerOptions. There are [pros and cons](https://dd.engineering/blog/typescript-the-skiplibcheck-option-explained) here, the pro being it's showing me clean-webpack-plug is out of date.
1. Drop `clean-webpack-plugin` and switch to [output:clean](https://webpack.js.org/guides/output-management/#cleaning-up-the-dist-folder) - though I'm not sure how well that works with webpack's express middleware yet.
1. Switch to Webpack 4.

