# Webpack Overview and Setup

Webpack is a module bundler for JavaScript applications. It processes and bundles all your files into optimized files for production. It handles bundling, module loading, code splitting, HMR (Hot Module Replacement), tree shaking, and asset optimization.

## Installing Webpack

First, you need to install `webpack` and `webpack-cli`:

`npm install --save-dev webpack webpack-cli`

Then, add a script in your `package.json` to run Webpack:

```json
{
  "scripts": {
    "start": "webpack"
  }
}
```

## Webpack Configuration File

### Entry

Webpack configuration includes an `entry` property where you can specify the files that will be bundled. You can define multiple entry points for different chunks.

```javascript
module.exports = {
  entry: {
    main: "./src/index.js",
    another: "./src/another-entry.js",
  },
};
```

### Output

The `output` property specifies the path and the name of the output file after bundling. You can specify the build folder (for example, `dist`) and the filename of the bundled file.

```javascript
module.exports = {
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "[name].bundle.js", // [name] will refer to the chunk name defined in entry
  },
};
```

### Loaders

Loaders are used to process different types of files, such as images and CSS. For example, if you want to load CSS and images correctly in your JavaScript bundle, you can use loaders like `css-loader` and `file-loader`.

`npm install --save-dev css-loader style-loader file-loader`

In the Webpack config:

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /\.(png|jpg|gif)$/,
        use: ["file-loader"],
      },
    ],
  },
};
```

### HTMLWebpackPlugin

To automatically generate an HTML file that includes the dynamic bundle address, you can use `html-webpack-plugin`. This plugin will generate an HTML file in the build folder and include the appropriate paths to your JavaScript bundles.

`npm install --save-dev html-webpack-plugin`

Webpack configuration with `HtmlWebpackPlugin`:

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      template: "./src/index.html", // The template HTML file
      filename: "index.html", // The generated HTML file
    }),
  ],
};
```

### Caching

To improve caching, you can add `[contenthash]` to the output file name. This ensures that if there are no changes in the file, Webpack won't generate a new bundle.

```javascript
module.exports = {
  output: {
    filename: "[name].[contenthash].js", // Use [contenthash] for caching
  },
};
```

### Dev Server and Hot Module Replacement (HMR)

To handle hot reloading and a live server, you can use Webpack Dev Server. This will automatically reload your browser whenever changes are made to the code.

`npm install --save-dev webpack-dev-server`

In the Webpack configuration:

```javascript
module.exports = {
  static: {
    directory: path.resolve(__dirname, "dist"),
    port: 3000,
    open: true,
    hot: true,
    compress: true,
    historyApiFallback: true,
  },
};
```

### Asset Modules

You can assign `assetModuleFilename` in the `output` configuration to determine how asset files (like images, fonts, etc.) will be named when they are output.

```javascript
module.exports = {
  output: {
    assetModuleFilename: "[name][ext]", // The [name] and [ext] represent the file name and extension
  },
};
```

### MiniCssExtractPlugin

The `MiniCssExtractPlugin` is used to extract CSS into separate files, which are then linked in the HTML, helping to prevent issues with styles not being applied correctly.

`npm install --save-dev mini-css-extract-plugin`

In the Webpack configuration:

```javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css", // Extracted CSS file name
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
      },
    ],
  },
};
```

### Minifying HTML, CSS, and JS

You can use plugins to minify your files for production to improve performance. For example, you can use `TerserPlugin` for JavaScript and `OptimizeCSSAssetsPlugin` for CSS.

Install the necessary plugins:

`npm install --save-dev terser-webpack-plugin optimize-css-assets-webpack-plugin`

In the Webpack configuration:

```javascript
const TerserPlugin = require("terser-webpack-plugin");
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  optimization: {
    minimizer: [
      new OptimizeCssAssetsPlugin(),
      new TerserPlugin(),
      new HtmlWebpackPlugin({
        template: "./src/template.html",
        minify: {
          removeAttributeQuotes: true,
          collapseWhitespace: true,
          removeComments: true,
        },
      }),
    ],
  },
};
```

### Using Babel Loader

To ensure compatibility with older browsers, you can use `babel-loader` and the `@babel/preset-env` to transpile modern JavaScript (ES6+) to ES5.

`npm install --save-dev babel-loader @babel/core @babel/preset-env`

In the Webpack configuration:

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"],
          },
        },
      },
    ],
  },
};
```

### Merge Configurations

You can merge Webpack configurations for different environments (development and production) using the `webpack-merge` library. This allows you to share common configurations and have separate configurations for development and production.

`npm install --save-dev webpack-merge`

### Example of Common, Dev, and Prod Configs

#### Common Config (`webpack.common.js`):

```javascript
module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "[name].bundle.js",
  },
};
```

#### Development Config (`webpack.dev.js`):

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");

module.exports = merge(common, {
  devServer: {
    contentBase: "./dist",
    hot: true,
  },
  devtool: "source-map", // for debug purpose
});
```

#### Production Config (`webpack.prod.js`):

```javascript
const merge = require("webpack-merge");
const common = require("./webpack.common");
const TerserPlugin = require("terser-webpack-plugin");
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");

module.exports = merge(common, {
  optimization: {
    minimize: true,
    minimizer: [new TerserPlugin(), new OptimizeCSSAssetsPlugin()],
  },
});
```
