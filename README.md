## ladevperfhttp2
###1. Web Performace
####2 Where can we optimize perf
Technical performance: The time it takes for every asset to be downloaded and displayed correctly in the browser  
Preceived performance: The time it takes for the user to get a functional experience that makes them think the site is fully loaded  

###2. Web Perf Vis
####1 A look at how
1 The page will not be rendered before all CSS is downloaded and the CSSOM is complete  
2 The visitor will not get the full interactive exp until all js is loaded which can only happed when both DOM and CSSOM are complete  

1.1 perf opt:
- inline critical CSS
- call js async
- concat css and js


http/2:  
download everything(except html) simulty.  
modularize minified css and js in smaller files  
cache css and js that is unlikely to change

####4 How to measure performance
multi test:
pingdom, gtmetrix

###3. Optimizing Images
####2 Image format options
GIF:never  
jpeg: photos  
png: static computer-generated graphics  
svg: computer-generated graphics

####3 Manual image optimization
1 blur unnessary areas  
2 size image to match largest possible display  
The number one rule of .JPEG compression is the more details the image contains, the larger the file gets.  
In other words, an image with a low depth of field, or high bokeh, will produce a smaller file size. 

####5 Sidebar, gulp postcss precss and autoprefixer
```
gulp.task('css',function(){
  gulp.src(source+'**/*.css').pipe(postcss([
    precss(),autoprefixer()])).pipe(gulp.dest(dest));
});

//gulp-serserver
gulp.task('webserver',function(){
  gulp.src(dest).pipe(webserver({
    livereload:true,
    open:true
  }))
})
```
####6 Automated image optimiza
```
var image = require('gulp-image');
gulp.task('imageopt',function(){
  gulp.src(source+'img/**/*.{jpg,JPG,png}').pipe(image()).pipe(gulp.dest(dest)+'images'));
})
```

###4. Markup and Content
####1 Code optimization in an HTTP/2
code optimization landmarks:
- minify everything
- Concatenation of CSS/JS is anti-pattern
- critical loading matters, defer/async when you can


####2 Automated minification of HTML, CSS, and JavaScript
gulp-minify cssnano gulp-htmlmin (github:html-minifier)
```
var htmlmin  =require('gulp-htmlmin'),
minify = require('gulp-minify');
cssnano = require('css-nano')
gulp.task('html',()=>{
  gulp.src(source+'*.html').pipe(htmlmin({
    collapseWhiteSpace:true,
    minifyJS:true,
    removeComments: true
  })
})

gulp.task('js',()=>{
  gulp.src(source+'*.js').pipe(minify()
  ).pipe(gulp.dest(dest+'js'));
})

gulp.task('css',function(){
  gulp.src(source+'**/*.css').pipe(postcss([
    precss(),autoprefixer(),cssnano()])).pipe(gulp.dest(dest));
});
```

####3 Modularize CSS for HTTP/2
1:40
in style-main.css, remove all modules except variable module.  
in module folder, remove all  _ in filenames,  
then add all css files in main page

####4 Deferring noncritical CSS
