1. Створюємо папку src
2. Додаємо до src index.html
3. Додаємо до src index.js
4. Додаємо до src index.css
5. Додаємо до src папки img, fonts
<!-- 6. Створюємо package.json
   "type": module -->
6. npm i webpack webpack-cli webpack-dev-server -D
7. Налаштовуємо scripts
   "start": "webpack serve",
   "build-dev": "webpack",
   "build-prod": "webpack --node-env=production",
   "clear": "rd /s /q dist"
8. Створюємо webpack.config.js
   const path = require('path');

   module.exports = {}

9. Прописуємо entry та output
   entry: path.resolve(**dirname, "src", "index.js"),
   output: {
   path: path.resolve(**dirname, "dist"),
   clean: true,
   filename: "index.[contenthash].js",
   },
10. Прописуємо константи режиму розробки
    const mode = process.env.NODE_ENV || "development";
    const devMode = mode === "development";
    const target = devMode ? "web" : "browserslist";
    const devtool = devMode ? "source-map" : undefined;
11. Створюємо файл .browserslistrc в корні
12. Додаємо до файла .browserslistrc таку інфу
    last 2 versions
    not dead
    > 0.5%
13. Додаємо до export default{} (згори перед entry)
    mode,
    target,
    devtool,
14. Для роботи з html встановлюєм плагін
    npm i html-webpack-plugin -D
15. Прописуємо імпорт для плагіну
    const HtmlWebpackPlugin = require('html-webpack-plugin');
16. Створюємо в export default поле plugins та додаємо цей плагін
    plugins: [
    new HtmlWebpackPlugin({
    template: path.resolve(__dirname, "src", "index.html"),
    }),
    ],
17. Для автоматичного оновленя змін в html встановлюємо html-loader
    npm i html-loader -D
    - Створюємо поле module (після плагинів), там прописуємо правило
      module: {
      rules: [
      { test: /\.(html)$/i, loader: "html-loader" },
      ],
      },
    - Додаємо імпорт до index.js щоб відстежувати зміни
      import './index.html';
18. Для стилей встановлюємо лоадери
    - npm i style-loader css-loader -D
    - прописуємо в rules:
      { test: /\.(c|sa|sc)ss$/i, use: ["style-loader" , "css-loader"] },
    - імпортуємо index.css до index.js
      import './index.css';
19. За замовчуванням стилі додаються до html файлу. Щоб формувати окремий файл, використовуємо плагін MiniCssExtractPlugin
    - npm install --save-dev mini-css-extract-plugin
    - до файлу конфігурації додаємо імпорт
      const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    - до розділу plugins додаємо
      new MiniCssExtractPlugin({ filename: "[name].[contenthash].css" })
    - тепер цей розділ буде такий
      plugins: [
      new HtmlWebpackPlugin({
      template: path.resolve(\_\_dirname, "src", "index.html"),
      }),
      new MiniCssExtractPlugin({ filename: "[name].[contenthash].css" })
      ],
    - для продашену/деву будемо використовувати різні режими:
      { test: /\.(c|sa|sc)ss$/i, use: [devMode ? "style-loader" : MiniCssExtractPlugin.loader, "css-loader"] },
20. Налаштування препроцесорів
    - перейменуємо файл index.css на index.scss
    - встановимо лоадер npm i sass-loader sass -D
    - додамо цей лоадер
      { test: /\.(c|sa|sc)ss$/i, use: [devMode ? "style-loader" : MiniCssExtractPlugin.loader, "css-loader", "sass-loader"] },
    - перейменуємо index.css -> index.scss Також не забудьте змінити розширення css->scss в файлі index.js
    - створимо папку style
    - додамо туди два файла \_var.scss, \_style.scss підключемо іх до import.scss
      @import 'style/var';
      @import 'style/style';
    - туди ж можемо підключати і сторонні файли наприклад normalize.css https://necolas.github.io/normalize.css/
21. Автопрефвкси для стилів

    - використовуємо postcss-loader
      npm i postcss-loader postcss postcss-preset-env -D
    - прописуємо loader в rules:
      {
      loader: 'postcss-loader',
      options: {
      postcssOptions: {
      plugins: [require('postcss-preset-env')],
      },
      },
      },

22. Налаштування сервера - додаємо перед entry
    devServer: {
    port: 3000,
    open: true,
    hot: true,
    }
23. Налаштування JavaScript(babel)
    - встановлюємо babael-loader
      npm install -D babel-loader @babel/core @babel/preset-env
    - додаємо до rules
      {
      test: /\.m?js$/i,
      exclude: /(node_modules|bower_components)/,
      use: {
      loader: 'babel-loader',
      options: {
      presets: ['@babel/preset-env']
      }
      }
      }
    - додавання babel/polyfill
      npm install --save @babel/polyfill
      треба додати його й до файлу конфігурації
      entry: ["@babel/polyfill", path.resolve(__dirname, "src", "index.js")],
24. Шрифти
    - додаємо в config
      {
      test: /\.woff2?$/i,
      type: "asset/resource",
      generator: {
      filename: "fonts/[name][ext]",
      },
      },
    - створюємо файл \_fonts.scss
    - додаємо в нього
      @font-face {
      font-family: 'Montserrat';
      src: url('fonts/montserrat-v23-latin_cyrillic-700.woff2') format('woff2'),
      url('fonts/montserrat-v23-latin_cyrillic-700.woff') format('woff');
      }
    - імпортуємо файл \_fonts.scss до index.scss
      @import 'style/fonts';
    - додаємо цей шрифт до style.scss
      body{
      font-family: 'Montserrat';
      }
25. Зображення
    - додати до rules
      {
      test: /\.(jpe?g|png|webp|gif|svg)$/i,
      type: "asset/resource",
      generator: {
      filename: "img/[name][ext]",
      },
      },
    - для стискання встановлюємо
      npm i image-webpack-loader -D
    - оновити rules
      {
      test: /\.(jpe?g|png|webp|gif|svg)$/i,
      use: [
      {
      loader: 'image-webpack-loader',
      options: {
      mozjpeg: {
      progressive: true,
      },
      optipng: {
      enabled: false,
      },
      pngquant: {
      quality: [0.65, 0.90],
      speed: 4
      },
      gifsicle: {
      interlaced: false,
      },
      webp: {
      quality: 75
      },
      }
      }
      ],
      type: 'asset/resource',
      },
