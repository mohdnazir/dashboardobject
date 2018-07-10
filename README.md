# Dashboard object alignment using JQuery
HTML Jquery to align objects on dashboard
```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Object Allignment on Dashboard</title>

  <link rel="stylesheet" type="text/css" media="screen" href="https://code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css" />
  <!-- Latest compiled and minified CSS -->
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
  <!-- Optional theme -->
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap-theme.min.css" integrity="sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp" crossorigin="anonymous">
  
  <script src="https://code.jquery.com/jquery-1.12.4.js"></script>
  <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>
  <!-- Latest compiled and minified JavaScript -->
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>

  <style>
    #feedback { font-size: 1.4em; }
    #container .ui-selecting { background: rgb(255, 233, 173); }
    #container .ui-selected { border: 2px solid rgb(18, 7, 167); color: black; }
    #container { list-style-type: none; margin: 0; padding: 0; width: 60%; }
    #container li { margin: 3px; padding: 0.4em; font-size: 1.4em; height: 18px; }
  </style>

</head>
<body >
  <script>
    $(function () {
      'use strict';
      var selected = $([]), offset = {top: 0, left: 0}, lastSelected = null;
      var multiItemJustDragged = false;
      $("#btnAlignTop").click(function(){
        
        if(lastSelected == undefined)
          return
        var top = $(lastSelected).offset().top;
        
        $(".ui-selected").not($(lastSelected)).each(function (i,e) {
          $(e).offset({top:top});
        });

      })      

      $("#btnAlignLeft").click(function(){
        if(lastSelected == undefined)
          return
        var left = $(lastSelected).offset().left;
        
        $(".ui-selected").not($(lastSelected)).each(function (i,e) {
          $(e).offset({left:left});
        });
      });

      $("#btnAlignBottom").click(function(){
        if(lastSelected == undefined)
          return;

        var baseItembottom = $(lastSelected).offset().top + $(lastSelected).height();
        
        $(".ui-selected").not($(lastSelected)).each(function (i,e) {
          var curItemBottom = $(e).offset().top + $(e).height();
          var newTop = $(e).offset().top + (baseItembottom-curItemBottom);
          $(e).offset({top:newTop});
        });

      });
      
      $("#btnAlignRight").click(function(){
        if(lastSelected == undefined)
          return;
        
        var baseItemRight = $(lastSelected).offset().left + $(lastSelected).width();
        
        $(".ui-selected").not($(lastSelected)).each(function (i,e) {
          var curItemRight = $(e).offset().left + $(e).width();
          var newLeft = $(e).offset().left + (baseItemRight-curItemRight);
          $(e).offset({left:newLeft});
        });

      });

      $("#btnEqualSize").click(function(){
        if(lastSelected == undefined)
          return;
        
        var baseItemWidth =  $(lastSelected).width();
        var baseItemHeight =  $(lastSelected).height();
        
        $(".ui-selected").not($(lastSelected)).each(function (i,e) {
          $(e).height(baseItemHeight);
          $(e).width(baseItemWidth);
        });

      });

      $("#btnAlighCenterHoriz").click(function(){
        if(lastSelected == undefined)
          return;
        
        var baseItemTop =  $(lastSelected).offset().top;
        var baseItemCenter =  ($(lastSelected).height()/2)+baseItemTop;
        
        $(".ui-selected").not($(lastSelected)).each(function (i,e) {
          var curItemTop = baseItemCenter - $(e).height()/2;
          $(e).offset({top:curItemTop});
        });
        
      });

      $("#btnAlighCenterVert").click(function(){
        if(lastSelected == undefined)
          return;
        
        var baseItemLeft =  $(lastSelected).offset().left;
        var baseItemCenter =  ($(lastSelected).width()/2)+baseItemLeft;
        
        $(".ui-selected").not($(lastSelected)).each(function (i,e) {
          var curItemLeft = baseItemCenter - $(e).width()/2;
          $(e).offset({left:curItemLeft});
        });        
      });

      $('.ui-widget-content').click(function(e){        
        if(e.ctrlKey){
          if ($(this).is(".ui-selected")) {
            $(this).removeClass("ui-selected");
            lastSelected = $(".ui-selected")[$(".ui-selected").length-1];
          }else{
            $(this).addClass("ui-selected");
            lastSelected = $(this);
          }
        }else{
          if(multiItemJustDragged){
            multiItemJustDragged = false;
            return;
          }
          $(".ui-selected").removeClass("ui-selected");
          $(this).addClass("ui-selected")
          lastSelected = $(this); 
        }

      })
           
      $("#container").selectable();

      $("#container div").resizable({
        //containment: "#container"
      });
      
      $("#container div").draggable({
        snap : false,
        containment: "parent", 
        start: function (e) {
          
          selected = $(".ui-selected").each(function () {
            var element = $(this);
            element.data("offset", element.offset());
          });
          
          //if only single item is selected and control key is not pressed
          //than remove previous selection and drag current selected item only
          if(selected.length == 1 && e.ctrlKey == false){
            $(".ui-selected").each(function () {
              $(this).removeClass("ui-selected");
            });
            selected = $(".ui-selected").each(function () {
              var element = $(this);
              element.data("offset", element.offset());
            });            
          }
          
          $(this).addClass("ui-selected");

          offset = $(this).offset();
        },
        drag: function (event, ui) {
          var topDiff = $(this).offset().top - offset.top;    //how much current element moved from vertically
          var leftDiff = $(this).offset().left - offset.left; //how much current element moved from horizontllay
          selected.not(this).each(function (i,e) {
              var element = $(e), offset = element.data("offset");              
              element.offset({top: offset.top+topDiff, left:offset.left+leftDiff});
            });
        },
        stop: function(event, ui){
          multiItemJustDragged = selected.length > 1 ? true : false;
          selected = null;
        }
      });
    });
  </script>
  <div class="container">
    <button id="btnAlignTop" class="btn btn-primary">Align Top</button>
    <button id="btnAlignLeft" class="btn btn-primary">Align Left</button>
    <button id="btnAlignBottom" class="btn btn-primary">Align Bottom</button>
    <button id="btnAlignRight" class="btn btn-primary">Align Right</button>
    <button id="btnEqualSize" class="btn btn-primary">Make Equal Size</button>
    <button id="btnAlighCenterHoriz" class="btn btn-primary">Center Horizontllay</button>
    <button id="btnAlighCenterVert" class="btn btn-primary">Center Vertically</button>
    <div class="well well-sm" style="margin-top: 5px">Ctrl+click or click and drag at empty area to select multiple item</div>
    <div id="container"  style="border:1px solid black; width: 100%; height: 500px;" >
      <div id="d1" class="ui-widget-content float-left" style="height: 150px; width: 150px;">
        D1
      </div>

      <div id="d2" class="ui-widget-content" style="height: 150px; width: 150px;">
        D2
      </div>
      <div id="d3" class="ui-widget-content" style="height: 150px; width: 150px;">
        D3
      </div>
    </div>
  </div>
</body>
</html>
```
