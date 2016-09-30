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
