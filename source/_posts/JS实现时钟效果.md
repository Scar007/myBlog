---
title: JS实现 时钟效果
subtitle: time
date: 2016-3-23
categories: 案例
---
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>js时钟效果</title>
        <style>
            .clock{
                width: 600px;
                height: 600px;
                margin: 50px auto;
                background:url(images/clock.jpg) no-repeat;
                position: relative;
            }
            .clock div{
                width: 600px;
                height: 600px;
                position: absolute;
                top: 0;
                left: 0;
            }
            .h{
                background: url(images/hour.png)no-repeat center center;
            }
            .m{
                background: url(images/minute.png)no-repeat center center;
            }
            .s{
                background: url(images/second.png)no-repeat center center;
            }
        </style>
    </head>
    <body>
        <div class="clock" id="clo">
            <div class="h"></div>
            <div class="m"></div>
            <div class="s"></div>
        </div>
    </body>
</html>
<script type="text/javascript">
    var clock = document.getElementById("clo");
    var h = 0, m = 0 , s =0, ms = 0;
    setInterval(function() {
        var date = new Date();
        ms = date.getMilliseconds();
        s = date.getSeconds()+ms/1000;
        m = date.getMinutes()+s/60;
        h = date.getHours() % 12+m/60;
        
        clock.children[0].style.WebkitTransform = "rotate("+h*30+"deg)";
        clock.children[1].style.WebkitTransform = "rotate("+m*6+"deg)";
        clock.children[2].style.WebkitTransform = "rotate("+s*6+"deg)";
    },50);
</script>
```


