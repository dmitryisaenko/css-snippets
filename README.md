# css-snippets

## 1.Relative paddings
>Ref: Youtube [5 CSS Tips & Tricks for better Responsive Web Design](https://youtu.be/2IV08sP9m3U?si=8w_c6h4ZqPadjK2d&t=43)

_Source code:_
```
.container {
    padding: 5em;
}
@media(max-width: 700px) {
    .container{
        padding: 2em;
    }
}
```

_Problem description:_

На моб.устройствах отступы будут визуально больше

_Solution code:_

```
.container {
    padding: min(5em, 8%);
}
```


## 2.Responsive font-sizes
>Ref: Youtube [5 CSS Tips & Tricks for better Responsive Web Design](https://youtu.be/2IV08sP9m3U?si=gHxIYRVkQd891FOH&t=125)

_Solution description:_

* Вместо `px` использовать `rem`
* rem - The root element's font-size
* vw - % from width:
  * Need fix for wild-monitor sizes
  * Need to fix for little-monitor sizes
* Better to use `clamp` - min, preffered, max
* For zoom-function is better to add `calc`

_Solution code:_
```
h1{
    font-size: clamp(1.8rem, 10vw, 5rem);
}
h1{
    font-size: clamp(1.8rem, calc(7vw + 1rem), 5rem);
}
```

## 3.Responsive Images
>Ref: Youtube [5 CSS Tips & Tricks for better Responsive Web Design](https://youtu.be/2IV08sP9m3U?si=733gd0aN4O0o1p2s&t=300)

_Solution description:_
![](/img/01a.jpg)
![](/img/01b.jpg)

_Solution code:_
```
img{
    max-width: 100%;
    height: auto;
    aspect-ratio: 9 / 16;
    object-fit: cover;
}
img{
    max-width: 100%;
    height: auto;
    aspect-ratio: 1 / 1;
    object-fit: cover;
}
```

## 4. 100vh
>Ref: Youtube [5 CSS Tips & Tricks for better Responsive Web Design](https://youtu.be/2IV08sP9m3U?si=trasQMjyTlEs2iAM&t=427)

_Problem description:_

When we use `100vh` and `margins = 0` it look goods on laptop, but cut down on the mobile screens

_Solution description:_

You need to use `dvh - dynamic viewvport height`:

![](/img/04a.jpg)


## 5.Opacity instead property:none
>Ref: Youtube [5 CSS Tips & Tricks for better Responsive Web Design](https://youtu.be/2IV08sP9m3U?si=Iem2dArvCaUpdSFR&t=501)

_Solution description:_

Instead `display:none` is to use `opacity:0` or `position:absolute` (it will be able to add transitions):

![](/img/05a.jpg)
![](/img/05b.jpg)

## 6.Пиксели в CSS не нужны — используй Rem! Адаптивный размер шрифта, Sass-миксин и функция Px to Rem
![](/img/06a.jpg)
>Ref: Youtube [Пиксели в CSS не нужны — используй Rem! Адаптивный размер шрифта, Sass-миксин и функция Px to Rem](https://www.youtube.com/watch?v=6l6IJLlSoLo)

>Ref: GitHub [pixels-vs-rem](https://github.com/aleksanderlamkov/pixels-vs-rem)
* Используя `rem` можно добиться, чтобы размер шрифта менялся при изменении маштаба браузра (при использовании `px` такого не будет)
* Чтобы перевести `px` в `rem` нужно значение в `px` разделить на 16
* Чтобы отступы и, например, радиусы также пропорционально изменялись, вместо `rem` нужно использовать `em` - это коефициент от `font-size` текущего, а не корневого элемента. 
* Чтобы получить значение `em` нужно размер в `px`, вычисленный для текущего элемента (или его атрибута, напр. отсуп) разделить на значение `font-size` корневого элемента, вычисленного тоже в `px`:

![](/img/06b.jpg)
* Вариант использования `rem` в Гридах (15,625 - это 250px) - в током случае при изменении машитаба все будет пропорционально:

![](/img/06c.jpg)
* Еще `rem` можно использовать в медиазапросах, а именно - в указании диапазона:

![](/img/06d.jpg)
* **НЕПРАВИЛЬНОЕ** использование `rem` - это в медиазапросах прописывать изменение корневого `font-size`:

![](/img/06e.jpg)
* Теперь **ПРАВИЛЬНОЕ** использование `rem`, с помощью препроцессора и миксинов:
![](/img/06f.jpg)

```
@use 'sass:math';

@mixin fluid-text($max: 48, $min: 16) {
  $maxViewportWidth: 1400;
  $minViewportWidth: 360;

  $maxSize: math.div($max, 16);
  $minSize: math.div($min, 16);
  $maxWidth: math.div($maxViewportWidth, 16);
  $minWidth: math.div($minViewportWidth, 16);

  $slope: math.div(($maxSize - $minSize), ($maxWidth - $minWidth));
  $yAxisIntersection: -$minWidth * $slope + $minSize;

  font-size: clamp(
    #{$minSize * 1rem},
    #{$yAxisIntersection * 1rem} + #{$slope * 100vw},
    #{$maxSize * 1rem}
  );
}

h1 { @include fluid-text(40, 28) }
h2 { @include fluid-text(36, 26) }
h3 { @include fluid-text(32, 24) }
h4 { @include fluid-text(28, 22) }
h5 { @include fluid-text(24, 20) }
h6 { @include fluid-text(20, 18) }
body { @include fluid-text(18, 16) }
```
* Преобразование значения из пикселей в Rem через SCSS-функцию:
```
@use 'sass:math';

@function rem($pixel) {
  @if math.is-unitless($pixel) {
    @return math.div($pixel, 16) + rem;
  } @else {
    @error 'Не используйте единицы измерения при работе с функцией rem()! Передавайте аргументом только числа.'
  }
}

.box {
  width: rem(600);        // 600px => 37.5rem
  height: rem(280);       // 280px => 17.5rem
  padding: rem(40);       // 40px  => 2.5rem
  border-radius: rem(15); // 15px  => 0.9375rem
  box-shadow:
    rem(6)                // 6px   => 0.375rem
    rem(6)                // 6px   => 0.375rem
    rem(10) deeppink;     // 10px  => 0.625rem

  /* 768px => 48rem */
  @media (width < rem(768)) {
    padding: rem(20);     // 20px  => 1.25rem
    font-size: rem(14);   // 14px  => 0.875rem
  }
}
```

## 7.БЭМ методология за 10 минут | Основы для начинающих
![](/img/07a.jpg)
>Ref: Youtube [БЭМ методология за 10 минут | Основы для начинающих](https://www.youtube.com/watch?v=FH-t1mt-lSs)

## 8.Ускоряем верстку | Гайд по препроцессору стилей | Sass (SCSS)
![](/img/08a.jpg)
>Ref: Youtube [Ускоряем верстку | Гайд по препроцессору стилей | Sass (SCSS)](https://www.youtube.com/watch?v=pBSch5AsJWs)

 Для VSCode оказывается есть расширение Live Sass Compiler: https://marketplace.visualstudio.com/items?itemName=ritwickdey.live-sass. Можно через него работать с SCSS также удобно, как в WebStorm.

![](/img/08b.jpg)

Миксины:

![](/img/08c.jpg)

Переменные в миксинах:

![](/img/08d.jpg)

Функции:

![](/img/08e.jpg)

Цикл:

![](/img/08f.jpg)

Сборка из разных файлов:

![](/img/08g.jpg)

* _normalize.scss

![](/img/08ga.jpg)

* _mixins.scss

![](/img/08gb.jpg)

* _variables.scss

![](/img/08gc.jpg)

* _utils.scss - перечисляются утилитарные функции, которые можно добавлять к различным элементам разметки

![](/img/08gd.jpg)

* _globals.scss - глобальные стили, характерные для текущего проекта

>Ref: GitHub [index.css](https://raw.githubusercontent.com/aleksanderlamkov/css-normalize/main/index.css)


![](/img/08ge.jpg)


## 9.How to make your website responsive
![](/img/09a.jpg)
>Ref: Youtube [How to make your website responsive)](https://www.youtube.com/watch?v=vQDgoQKfdzM)

[The Clamp Calculator](https://royalfig.github.io/fluid-typography-calculator/)

![](/img/09b.jpg)

![](/img/09c.jpg)

![](/img/09d.jpg)

![](/img/09e.jpg)