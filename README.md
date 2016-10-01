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
```
<noscript id="defer">
<link rel="css/lateload.css" media=all">
</noscript>
<script>
var loadDefer = function(){
  var addStyle = document.getElementById("defer");
  var replacement = document.createElement("div");
  replacement.innerHTML = addStyle.textContent;
  document.body.appendChild(replacement);
  addStyle.parentNode.removeChild(addStyle); //remove the noscript chunk
};
var rem = requestAnimationFrame;
if(rem){rem(function(){window.setTimeout(loadDefer,0);});
else wondow.addEventListener('load',loadDefer);
</script>
```

####5 JavaScript loading: Async and defer
 When the browser parses the page, it starts with the HTML and starts parsing it until it encounters the call for the JavaScript. Then it stops parsing the HTML while it downloads and then executes the JavaScript. Only after both the download and execution are complete does it continue parsing the HTML. And here you see why we usually place our JavaScripts at the bottom of the page, so we're not blocking the HTML parsing until the JavaScript kicks in.  
 
But we can change this behavior. If we append the async attribute to our script call, the way the browser parses the content changes. Now the browser will continue to parse the HTML while the JavaScript is downloading, and the only time the parsing stops is when the JavaScript is executed.  

The defer attribute kinda solves some of this, and it does it by completely deferring the execution of a JavaScript until everything else has happened. The other thing to know about defer is, when you use defer, the execution of the JavaScript will happen in the order the deferred elements are listed on the page.


###5. Optimizing Delivery
[h5bp config](http://github.com/h5bp/server-configs)

####4 Cache busting: Clearing long-term cached files
There's a really clever solution to this and it's called cache busting. We literally force the browser to download new files and we can do this in a really smart way by simply renaming the files any time we change them.  


gulp plugin: gulp-rev, gulp-rev-replace  
rev-del: delete replaced files from an old manifest  
04:30
```
rev = require('gulp-rev')
revReplace = require('gulp-rev-replace')
revDel = require('rev-del')

//change dest to limbo, then process by rev,revDel

gulp.task('revision'.['html','css','js'],function(){
  return gulp.src(limbo +'**/*.{js.css}').
  pipe(rev())
  .pipe(gulp.dest(dest))
  .pipe(rev.manifest())
  .pipe(revDel({dest:dest}))
  .pipe(gulp.dest(dest))
})


gulp.task('revreplace',['revision'],function(){
  var manifest = gulp.src(dest+'rev-manifest.json');
  return gulp.src(limbo+'**/*.html')
  .pipe(revReplace({manifest:manifest})
  .pipe(gulp.dest(dest));
})
```
####5 Push files to the browser using HTTP/2 Server Push
 Server Push is exactly what it sounds like. We can tell the server to push content to the browser before the browser makes a request for it. 
