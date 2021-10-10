# cyrill-martin.github.io

This repository contains the Vue.js files for my online CV accessible at [cyrill-martin.github.io](https://cyrill-martin.github.io).

The CV's content as well as the categories and values for the shown spider charts are maintained in the JSON file "myCv.json" in ``./src``. 
The charts are based on the D3.js code written in the "drawD3()" method in ``./src/components/stations/TheChart.vue``.

The repository makes use of the npm package gh-pages in order to push the built files to the gh-pages branch on GitHub from where a GitHub Pages site is being built.

## Dependencies

- npm

## Serve and Develop Locally

1. Clone repository
2. `cd` into repository
3. Run `npm install` to get the needed npm packages
4. Run `npm run serve` to serve on http://localhost:8080

## Build production files

Run `npm run build` to build files in a `dist` directory.

## Deploy to GitHub Pages

Run `npm run deploy` to build production files and push them to the gh-pages branch on GitHub. 

## License

MIT
