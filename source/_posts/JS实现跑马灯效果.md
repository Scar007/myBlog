---
title: JS实现跑马灯效果(鼠标滑入可暂停，离开继续跑)
subtitle: horserace
date: 2016-10-23
categories : 案例
---
```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>跑马灯效果</title>
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
            font-size: 16px;
        }

        #wrap {
            width: 1000px;
            margin: 20px auto;
            border: 2px solid green;
            white-space: nowrap;
            overflow: hidden;
            padding: 10px;
            color: rgb(110, 244, 216);
            background: #1c1c51;
        }

        #wrap div {
            display: inline-block;
            font-size: 20px;
        }

        #wrap span {
            font-size: 20px;
            color: red;
        }


    </style>
</head>
<body>
<div id="wrap">
    <div id="first">
        <span>周星驰</span>曾经有一份真诚的爱情放在我面前，我没有珍惜，等我失去的时候我才后悔莫及，人世间最痛苦的事莫过于此。 
如果上天能够给我一个再来一次的机会，我会对那个女孩子说三个字：我爱你。 
如果非要在这份爱上加上一个期限，我希望是…… 
一万年
    </div>
    <div id="last">
        <span>周星驰</span>曾经有一份真诚的爱情放在我面前，我没有珍惜，等我失去的时候我才后悔莫及，人世间最痛苦的事莫过于此。 
如果上天能够给我一个再来一次的机会，我会对那个女孩子说三个字：我爱你。 
如果非要在这份爱上加上一个期限，我希望是…… 
一万年
    </div>
</div>


<script type="text/javascript">

    (function () {
        var wrap = document.getElementById('wrap'), first = document.getElementById('first');
        var timer = window.setInterval(move, 5);
        wrap.onmouseover = function () {
            window.clearInterval(timer);
        };
        wrap.onmouseout = function () {
            timer = window.setInterval(move, 5);
        };
        function move() {
            wrap.scrollLeft++;
            if (wrap.scrollLeft >= first.scrollWidth) {
                wrap.scrollLeft = 0;
            }
        }
    })();

</script>
</body>
</html>
```





