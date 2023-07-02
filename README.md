# panorama(공개용)
<br>

## 1. 기본형

> 💡 mobile  
> 1. 이미지 중앙정렬
> 2. 사용자 컨트롤 가능


시연페이지 : [https://modangirlbin.github.io/pano](https://modangirlbin.github.io/pano/) (모바일)
<br>

### - scroll
    
```js
$('.img_wrap').scrollLeft(imgW/2);
```
    
### - swipe
    
```js
var swiper = new Swiper('.swiper-container', {
            slidesPerView: 'auto',    //동시에 보여줄 슬라이드 갯수
            freeMode: true,           //슬라이드 이동방식
            resistance : true,        
            resistanceRatio : 0,      //bounce
            //centeredSlides: true,
          });
```
    
    https://swiperjs.com/api/
    resistanceRatio : This option allows you to control resistance ratio
    
### - 오픈소스 커스텀
    
    반복해제x
    
<br>

## 2. 무한슬라이드

> 💡 mobile
> 1. 오토 슬라이드
> 2. 무한 루프
> 3. 사용자 컨트롤 가능

시연페이지 : https://modangirlbin.github.io/panorama (모바일)
<br>

### - scroll
    
    
### - swipe
    
 ```js
    var swiper = new Swiper('.swiper-container', {
      slidesPerView: 'auto',
      freeMode: true,
      loop: true,          //슬라이드 반복
    });
  ```
    
### - 오픈소스 커스텀
    
 ```js
    $(".panorama").panorama_viewer({
          repeat: true,
          autoPlay: true
        });
 ```
    
```js
    !function($){
      
        // touch이벤트 등록
        function touchHandler(event) {
          var touches = event.changedTouches,
              first = touches[0],
              type = "";
          
          // 마우스이벤트를 터치 이벤트로
          switch(event.type) {
            case "touchstart": type = "mousedown"; break;
            case "touchmove":  type="mousemove"; break;        
            case "touchend":   type="mouseup"; break;
            default: return;
          }
          
          simulatedEvent.initMouseEvent(type, true, true, window, 1,
            first.screenX, first.screenY,
            (first.clientX * mult), (first.clientY * mult), false,
            false, false, false, 0/*left*/, null);
            first.target.dispatchEvent(simulatedEvent);
          
          //속도조정
          var simulatedEvent = document.createEvent("MouseEvent");
          var mult = 3;
        }
      
        $.fn.panorama_viewer = function(options){
          
          document.addEventListener("touchstart", touchHandler, true);
          document.addEventListener("touchmove", touchHandler, true);
          document.addEventListener("touchend", touchHandler, true);
          document.addEventListener("touchcancel", touchHandler, true);
          
          return this.each(function(){
            var settings = $.extend({}, defaults, options),
                el = $(this);
            
            el.find("> img").load(function () {
              
              // dom추가
              el.find("> img").addClass("pv-pano");
              el.addClass("pv-container").wrapInner("<div class='pv-inner pv-animating'></div>");
              
              //이미지 wrapper에 대신 bg이미지설정(실제보여지는 이미지)
              el.find(".pv-inner").css({
                height: height,
                width: width,
                background: "url(" + imgSrc + ") 0 0 " + repeat,
                "background-size": "cover"
              })
      
              //auto
              function auto(){
                var _auto;
                
                return {
                  play: function() {
                    if(settings.autoPlay == true) {
                      _auto = setInterval(autoplay, 100);
                      function autoplay() {
                        var bgX = $bg.css('background-position-x');
                        var bgX_num = Number(bgX.slice(0, -2));
                        $bg.css('background-position-x',bgX_num-10+"px");
                      };
                      
                      return _auto;
                    }
                  },
                  stop: function() {
                    if(settings.autoPlay == true) {
                      clearInterval(_auto);
                    }
                  }
                }
              }
    
              //이동
              function move (e){
                
                var inbounds = {x: false, y: false},
                    offset = {
                      x: start.x - (origin.x - e.clientX),
                      y: start.y - (origin.y - e.clientY)
                    };
                if (settings.direction == "horizontal") {
                  if (settings.repeat == true) {
                    inbounds.x = true;
                  } else {
                    inbounds.x = offset.x < 0 && (offset.x * -1) < bounds.w;
                  }
                  if (movecontinue && inbounds.x) {
                    start.x = offset.x;
                    start.y = 0;
                  }
                } 
                
                var bgposx = $(this).css('background-position-x');
                var num_bgposx = bgposx.slice(0,-2);
      
                $(this).on('mousemove', function(){
                  if(num_bgposx<=1){
                    $(this).css('background-position', start.x + 'px ' + start.y + 'px');
                  }
                });
                
                origin.x = e.clientX;
                origin.y = e.clientY;
                
                e.stopPropagation();
                return false;
              }
    
              var toggleAuto = auto();
              
              function handle (e){
                movecontinue = false;
                $bg.unbind('mousemove', move);
                
                if (e.type == 'mousedown') {
                  toggleAuto.stop();
                  origin.x = e.clientX;
                  origin.y = e.clientY;
                  movecontinue = true;
                  $bg.bind('mousemove', move);
                } else {
                  $(document.body).focus();
                  toggleAuto.play();
                }
                
                e.stopPropagation();
                return false;
              }
              
              function reset (){
                start = {x: 0, y: 0};
                $(this).css('backgroundPosition', '0px 0px');
              }
              
              $bg.bind('mousedown mouseup mouseleave', handle);
              $bg.bind('dblclick', reset);
              if(settings.autoPlay == true){
                toggleAuto.play(); 
              }
              el.find(".pv-pano").hide()
            })      
          }); 
        }
      }(window.jQuery);
```
    
    정확한 좌표 값을 얻을 수 있는 마우스 이벤트와 달리 터치 이벤트는 손가락 접촉면이 크기 때문에 접촉 표면의 평균 좌표 값을 얻는다
    

### 테스트 환경

- IOS 12.4/13.3  (iphone se /iphone pro)
android 8.0 /10.0 (갤럭시s7 /s10+)
