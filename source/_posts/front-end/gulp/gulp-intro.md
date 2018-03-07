---
title: gulp 使用介绍
date: 2017-12-14 10:20:55
categories: "Front End"
tags:
    - Front End
    - gulp
keywords: Front End, gulp
---

先把坑挖好……

<!-- more -->

## 初识

全局安装

```
$ sudo npm install gulp -g
```

定位到 **项目根目录**，作为项目的开发依赖（devDependencies）安装

```
$ npm install gulp --save-dev
```

在项目根目录下创建一个名为 `gulpfile.js` 的文件

```
$ touch gulpfile.js
```

编辑文件，添加 gulp 任务，其中 `default` 为默认任务

```
var gulp = require('gulp');

gulp.task('default', function() {
    console.log('default run');
});

gulp.task('hello', function() {
    console.log('Hello World');
});
```

直接运行 gulp 会执行默认任务（等价于执行 `gulp default`）

```
$ gulp
[17:21:09] Using gulpfile ~/test/gulp/gulpfile.js
[17:21:09] Starting 'default'...
default run
[17:21:09] Finished 'default' after 130 μs
```

执行指定任务（如 `hello`），则可使用如下命令

```
$ gulp hello
[17:20:42] Using gulpfile ~/test/gulp/gulpfile.js
[17:20:42] Starting 'hello'...
Hello World
[17:20:42] Finished 'hello' after 126 μs
```

## Babel

- [Babel](http://babeljs.io/)

> Babel is a JavaScript compiler. Use next generation JavaScript, today.

Install the Babel CLI and a preset
- 安装 `babel-cli` 和 `babel-preset-env`

```
npm install --save-dev babel-cli babel-preset-env
```

Create a .babelrc file (or use your package.json)

```json
{
  "presets": ["env"]
}
```

Current package.json

```json
{
  "devDependencies": {
    "babel-cli": "^6.26.0",
    "babel-preset-env": "^1.6.1",
    "gulp": "^3.9.1"
  }
}
```

Current gulpfile
- 必须命名为 `gulpfile.babel.js` 否则无法正确识别

```
import gulp from 'gulp';

gulp.task('default', () => {
    console.log("Default");
});
```

```
$ gulp
[17:14:34] Failed to load external module @babel/register
[17:14:34] Requiring external module babel-register
[17:14:35] Using gulpfile ~/test/gulp/gulpfile.babel.js
[17:14:35] Starting 'default'...
Default
[17:14:35] Finished 'default' after 120 μs
```

## 应用

```
const gulp = require('gulp');
// scss to css
const gulpSass = require('gulp-sass');
// concat files
const gulpConcat = require('gulp-concat');
// minify css
const gulpMinifyCss = require('gulp-minify-css');

gulp.task('style', function () {
   return gulp.src('./app/style/**/*.scss')
       .pipe(gulpSass())
       .pipe(gulpConcat('core.min.css'))
       .pipe(gulpMinifyCss())
       .pipe(gulp.dest('./dest/style'));
});

gulp.task('default', function () {
   console.log('Default Task Run ...');
   gulp.run('style');
   // Watch directory setting, after changed, then run task 'style'
   gulp.watch('./app/style/**/*.scss', ['style']);
});
```

`gulp-load-plugins` and `gulp.run() has been deprecated.`

```
const gulp = require('gulp');
const gulpLoadPlugins = require('gulp-load-plugins');

const plugins = gulpLoadPlugins();

gulp.task('style', function () {
   return gulp.src('./app/style/**/*.scss')
       .pipe(plugins.sass())
       .pipe(plugins.concat('core.min.css'))
       .pipe(plugins.minifyCss())
       .pipe(gulp.dest('./dest/style'));
});

gulp.task('default', ['style'], function () {
   gulp.watch('./app/style/**/*.scss', ['style']);
});```



