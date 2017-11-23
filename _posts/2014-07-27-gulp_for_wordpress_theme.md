---
layout: post
title: wordpressのtheme開発を自動化するgulpの設定
categories: tech
---

wordpressのテーマを作成する際、sassのプリコンパイルやブラウザのリロードをgulpでやると便利なので備忘録として設定を残しておく。

gulpとは様々なタスクを自動化するビルドシステムである。

ここで紹介するgulpのビルドレシピは次のことを自動で行う。

* sassやJavascriptの変更を検知し、自動的にコンパイル・minifyを行う
* css, js, phpファイルの変更を検知し、ブラウザを更新する
* browser-syncを利用することで、ページ遷移、スクロール、フォームへの入力をブラウザ間で同期する

gulpのインストール方法はここでは説明しない。ググってくれ。

gulpの設定はこんなん。

```js
// Load plugins
var gulp = require('gulp'),
  plugins = require('gulp-load-plugins')({ camelize: true }),
  browserSync = require('browser-sync');

// Styles
gulp.task('styles', function() {
  return gulp.src('assets/styles/*.scss')
  .pipe(plugins.rubySass({ sourcemap: true, compass: true }))
  .pipe(plugins.autoprefixer('last 2 versions', 'ie 9', 'ios 6', 'android 4'))
  .pipe(gulp.dest('assets/styles/build'))
  .pipe(plugins.minifyCss({ keepSpecialComments: 1 }))
  .pipe(gulp.dest('./'))
  .pipe(browserSync.reload( { stream: true } ))
  .pipe(plugins.notify({ message: 'Styles task complete' }));
});

// Site Scripts
gulp.task('scripts', function() {
  return gulp.src(['assets/scripts/*.js'])
  .pipe(plugins.plumber())
  .pipe(plugins.jshint('.jshintrc'))
  .pipe(plugins.jshint.reporter('default'))
  .pipe(plugins.concat('main.js'))
  .pipe(gulp.dest('assets/scripts/build'))
  .pipe(plugins.rename({ suffix: '.min' }))
  .pipe(plugins.uglify())
  .pipe(gulp.dest('scripts'))
  .pipe(browserSync.reload( { stream: true } ))
  .pipe(plugins.notify({ message: 'Scripts task complete' }));
});


gulp.task('bower', function() {
  plugins.bowerFiles()
  .pipe(gulp.dest('./js/vendor'));
});

// Images
gulp.task('images', function() {
  return gulp.src('assets/images/**/*')
  .pipe(plugins.plumber())
  .pipe(plugins.cache(plugins.imagemin({ optimizationLevel: 7, progressive: true, interlaced: true })))
  .pipe(gulp.dest('assets/images'))
  .pipe(browserSync.reload( { stream: true } ))
  .pipe(plugins.notify({ message: 'Images task complete' }));
});

gulp.task('php', function() {
  return gulp.src('**/*.php')
  .pipe(browserSync.reload( { stream: true } ))
  .pipe(plugins.notify({ message: 'PHP task complete' }));
});

// browser-sync
gulp.task('bs', function() {
  browserSync({
    proxy: "192.168.33.10", // ここにwordpressサーバへのIPアドレスを書く
    minify: false
  });
});


// Watch
gulp.task('watch', function() {
  // Watch .scss files
  gulp.watch('assets/styles/**/*.scss', ['styles']);

  // Watch .js files
  gulp.watch('assets/scripts/**/*.js', ['plugins', 'scripts']);

  // Watch image files
  gulp.watch('assets/images/**/*', ['images']);

  // Watch image files
  gulp.watch('./**/*.php', ['php']);
});

// Default task
gulp.task('default', ['styles', 'scripts', 'images', 'bower', 'watch', 'bs']);
```
