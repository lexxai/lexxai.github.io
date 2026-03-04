---
layout: post
title: "SIMILE Timeline - коректування формату виводу для тексту підпису у події"
date: 2012-12-31 20:20:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/12/simile-timeline.html
---

Є такий засіб для відображення події у часі : [SIMILE Timeline](http://www.simile-widgets.org/timeline/) - Web Widget for Visualizing Temporal Data. Написаний на JavaScript.  
  
Якщо використовувати версію [timeline](http://code.google.com/p/simile-widgets/source/browse/timeline) 2.3.1 то відображення може бути  у ось такому вигляді:  
  

[![](/assets/images/blog/b0d580b53f89ef1e-a31a1faad92ac6ef.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjCoIssfWzOyX3lsAIafLLO8tfE-6JXH3qBRmvKQ3jfCo9DvT5P2s2w1-oRjzkk4dYrXSjEvVR2OgT6p7KzvflNs1hrQVQK9f-HsdqeK9NAdylMzo6hMNaZbp0SvaHu8joesCIDXazi4z_7/s1600/timeline-0.PNG)  
*Базове відображення*

  
  
  
Якщо, налаштувати тему так :  
 var theme = Timeline.ClassicTheme.create();  
 theme.event.tape.height = 15;  
  

[![](/assets/images/blog/41efe63510f10bc0-8e59b72eecef852f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxNM9I87eHMijuEAbMO5WidnJ-iyf9K9qcOX1uPyot1_XvXrXxrlvwkrTkRbx5-XVU1h652_f7P294is3inF7jf62Q59tWBcFa2GjVMyuCB9t7fRB8whxcgaIIEbpcpo28WcXotAUGqozP/s1600/timeline-1.PNG)  
*theme.event.tape.height = 15*

А якщо потрібно щоб підписи були у тому рядку що і події, то я зробив модифікацію для функції Timeline.OriginalEventPainter.  
  
  
patch  
#cd html/js/timeline/api/scripts   
# diff original-painter.js original-painter.js.0  
347,353c347  
<       var labelLeft;  
<     if ((startPixel+labelSize.width) > endPixel){  
<               labelLeft = endPixel;  
<     }else{  
<       labelLeft = startPixel;  
<     }  
<  
---  
>     var labelLeft = startPixel;  
358,361c352,354  
<     //var labelTop = Math.round(metrics.trackOffset + track \* metrics.trackIncrement + theme.event.tape.height);  
<  
<     var labelTop = Math.round(metrics.trackOffset + track \*  metrics.trackIncrement);  
<  
---  
>     var labelTop = Math.round(  
>         metrics.trackOffset + track \* metrics.trackIncrement + theme.event.tape.height);  
>  
512c505  
<     var color = '#fff';//evt.getTextColor();  
---  
>     var color = evt.getTextColor();  
  
  
***html/js/timeline/api/scripts/original-painter.js: ст***  
Timeline.OriginalEventPainter.prototype.paintPreciseDurationEvent = ...  
....    
 var labelLeft;  
 if ((startPixel+labelSize.width) > endPixel){  
  labelLeft = endPixel;  
 }else{  
  labelLeft = startPixel;>  
 }  
 var labelRight = labelLeft + labelSize.width;  
 var rightEdge = Math.max(labelRight, endPixel);  
 var track = this.\_findFreeTrack(evt, rightEdge);  
 var labelTop = Math.round(metrics.trackOffset + track \* metrics.trackIncrement);  
...   
  
  
***index.html:***  
....  
<script>  
   Timeline\_ajax\_url="/js/timeline/timeline\_ajax/simile-ajax-api.js?bundle=true";  
   Timeline\_urlPrefix="/js/timeline/timeline\_js/";  
   Timeline\_parameters='bundle=true';  
 </script>  
 <script src="/js/timeline/timeline\_js/timeline-api.js" type="text/javascript"></script>  
 <script src="/js/timeline/api/scripts/original-painter.js" type="text/javascript"></script>  
 var theme = Timeline.ClassicTheme.create();  
 theme.event.tape.height = 15;  
  
 var d = Timeline.DateTime.parseGregorianDateTime(new Date())  
  
      var bandInfos = [  
            Timeline.createBandInfo({  
                showEventText:  false,  
                width:          "60%",  
                intervalUnit:   Timeline.DateTime.DAY,  
                intervalPixels: 200,  
                eventSource:    eventSource,  
                date:           d,  
                theme:          theme,  
                layout:         'original'  // original, overview, detailed  
            }),  
            Timeline.createBandInfo({  
                width:          "20%",  
                intervalUnit:   Timeline.DateTime.MONTH,  
                intervalPixels: 200,  
                eventSource:    eventSource,  
                date:           d,  
                theme:          theme,  
                layout:         'overview'  // original, overview, detailed  
            }),  
            Timeline.createBandInfo({  
                width:          "20%",  
                intervalUnit:   Timeline.DateTime.YEAR,  
                intervalPixels: 200,  
                eventSource:    eventSource,  
                date:           d,  
                theme:          theme,  
                layout:         'overview'  // original, overview, detailed  
            })  
        ];  
  
        bandInfos[1].syncWith = 0;  
        bandInfos[2].syncWith = 1;  
        bandInfos[1].highlight = true;  
        tl = Timeline.create(document.getElementById("tl"), bandInfos);  
...  
<body onload="onLoad()">  
  <div id="body">  
    <h1>Timeline</h1>  
    <div id="tl" class="timeline-default dark-theme" style="height: 650px; margin: 2em;">  
    </div>  
        <noscript>  
        This page uses Javascript to show you a Timeline. Please enable Javascript in your browser to see the full page. Thank you.  
        </noscript>  
  </div>  
</body>  
</html>  
  
  
  

[![](/assets/images/blog/f6330a9246643094-a9120dba6339e442.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhI5PjQwDsrx9WZaG342E70-S6oXS9WiSOT08k8KIzOa6IEkkgQVVGsz09GYgOBs_acg9PscK6OcTZT9TediCiEzggWkmbIt1FPD4UnivWRvDq3K8FyonwJBRVPFIau94sObM7ael6SDY6g/s1600/timeline-2.PNG)  
*Після модифікації  функції Timeline.OriginalEventPainter*
