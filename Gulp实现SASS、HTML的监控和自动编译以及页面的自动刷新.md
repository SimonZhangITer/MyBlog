> Gulp是一款当前很流行的前端自动化构建工具

#功能
先说明一下本文所要实现的功能：

- gulp本地服务器
- 多个sass文件汇总为一个css并压缩
- <font color=red>自动监控和编译</font>SASS和HTML文件
- HTML文件修改后保存则<font color=red>自动刷新</font>页面显示。

#配置

首先在项目目录下面创建package.json和gulpfile.js两个文件。

看一下本地的项目目录结构：

<img src="http://static.oschina.net/uploads/space/2016/1110/184218_04Y7_2493500.png" width="300px">

##package.json

```json
{
    "name": "exam",
    "version": "1.0.0",
    "description": "examWeb",
    "main": "gulpfile.js",
    "dependencies": {},
    "devDependencies": {
        "gulp": "^3.9.0",
        "gulp-connect": "^2.3.1",
        "gulp-sass": "^2.3.2",
        "gulp-rename": "^1.2.2",
        "gulp-minify-css": "^1.2.4",
    },
    "scripts": {
        "test": "test"
    },
    "repository": {
        "type": "git",
        "url": "https://git.coding.net/xxx/xxx.git"
    },
    "keywords": [
        "exam",
        "angular",
        "web",
        "bootStrap"
    ],
    "author": "Simon Zhang",
    "license": "ISC"
}

```

- devDependencies就是所需要的插件
- repository.url是项目的git地址，也可不填

##gulpfile.js

备注直接写在文件里了：

```javascript
var gulp = require('gulp');
var connect = require('gulp-connect');
var sass = require('gulp-sass');
var minifyCss = require('gulp-minify-css');
var rename = require('gulp-rename');
//以上是项目需要引入的插件

var buildConfig = {
    IS_RELEASE_BUILD: true,
    styleDir: 'web/css',//导出的css目录
    htmlDir: 'web/**/*.html',//监控的HTML文件
    watchSass: 'web/**/*.scss'//监控的sass文件  **表示所有
};

gulp.task('server', function() {
    connect.server({
        livereload: true  //实时刷新
    });
});

gulp.task('start', ['server', 'watch-sass', 'watch-html']);
gulp.task('default', ['server', 'watch-sass', 'watch-html']);

gulp.task('watch-sass', ['sass'], function(donw) {
    gulp.watch(buildConfig.watchSass, ['sass']);
    console.log("====== watching hec sass files... =====");
});

gulp.task('sass', function(done) {
    gulp.src("web/exam.scss")
        .pipe(sass())
        .pipe(gulp.dest(buildConfig.styleDir))
        .pipe(minifyCss({
            keepSpecialComments: 0
        }))
        .pipe(rename({
            extname: '.min.css'
        }))
        .pipe(gulp.dest(buildConfig.styleDir))
        .on('end', done);
});

gulp.task('watch-html', function() {
    gulp.watch(buildConfig.htmlDir, ['html']);
});

gulp.task('html', function() {
    gulp.src(buildConfig.htmlDir)
        .pipe(connect.reload());
});

```

#使用

```bash
gulp 或者 gulp start
```
一键开启本地服务器、sass和html的监控以及编译、页面的自动刷新。
Server started http://localhost:8080

```bash
gulp server
```
单独开启本地服务器

```bash
gulp watch-sass
```
单独开启sass监控和自动编译

```bash
gulp watch-html
```
单独开启html的监控和自动编译以及自动刷新服务